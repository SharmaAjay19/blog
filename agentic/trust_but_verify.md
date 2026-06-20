# Trust but Verify — Except Nobody Built the Verify Part

*Notes on the quiet failure modes of working with AI coding agents — and the single gap underneath all of them*

*Ajay Sharma — Principal Software Engineer, Microsoft CoreAI*

*Part 1 of a two-part series. [Part 2](#read=building_the_verify_part.md) proposes the fix: the two artifacts that close the gap.*

---

You have probably already done this.

You hand a task to an AI coding agent. It works for a while, narrates its progress in a long and confident transcript, and produces a pull request. The diff is clean. The tests are green. The description is well-written — maybe a little *too* well-written. You skim it, you nod, you merge.

And somewhere in the back of your mind a small voice asks a question you cannot actually answer: *is this correct?*

Not "did it compile." Not "did the tests pass." **Correct.** Does it do the thing you actually needed — without quietly breaking something else, without paying a hidden cost at scale, without making the next person's change harder? You don't know. You got a pull request and a transcript. What you did not get was a way to *check*.

This article is about that gap. It is not a list of solutions. It is an attempt to name, clearly, the failure modes I keep hitting when I build software with AI agents — the ones that are easy to feel and hard to articulate — and to point at the one structural problem sitting underneath all of them.

If you have shipped anything with these tools, I think you will recognize every one of these.

> **Key takeaway —** The agent's job ends at "a pull request exists." Yours begins at "is it right?" — and almost nothing in the current workflow is designed to help you answer that.

---

## The asymmetry nobody priced in

For most of software's history, writing code and trusting code were roughly coupled. Producing a change took effort, and that effort *was* a big part of how you came to believe it worked. You held the problem in your head long enough to build a mental model of it.

AI agents broke that coupling. Generating a plausible change is now nearly free. Convincing yourself it is *actually right* costs exactly as much as it always did — arguably more, because you no longer did the thinking that used to come bundled with the typing.

So we industrialized the cheap half of the work and left the expensive half entirely on the human. The result shows up in the data. Adoption of AI coding tools keeps climbing, yet surveys from the same period show developers' *trust* in the output drifting the other way. One analysis found that senior engineers can actually come out behind, because the time they spend verifying machine-written code exceeds the time it saved them. Generation went up. Confidence went down. That is not a paradox — it is what happens when you scale production without scaling verification.

> **Key takeaway —** Code generation got cheap; validation, review, and accountability did not. Every failure mode below is a symptom of optimizing the half we made free and ignoring the half we left expensive.

---

## Two things the agent cannot see

Before the catalog of specific failures, it helps to understand *why* they cluster. In my experience almost all of them trace back to two structural facts about how these agents operate.

**First: the agent grades its own homework with the cheapest rubric it can find.** Left to its own devices, an agent optimizes for the most visible success signal available: it compiles, the tests are green, the output looks plausible. When that is the *only* scoreboard, the path of least resistance is not "make the system correct" — it is "make the scoreboard say green." Sometimes those are the same thing. Often they are not.

**Second: the agent reviews the diff, but correctness lives in the system.** An agent reasons about the text it just changed. But the properties you actually care about — performance under load, who depends on this, whether it fits the architecture, whether it matches production reality — are *off-diff*. They live in the running system, in other teams' code, in last week's incident, in a dashboard nobody linked. An agent that only inspects what it touched is structurally blind to all of it.

Hold those two facts in mind: a cheap rubric, and diff-bound vision. Almost everything that follows is one of those two failing in a new costume — plus a third failure that isn't about sight at all, which I'll come to.

> **Key takeaway —** The agent sees what it changed, not what it affected — and it is rewarded for a green checkmark, not a correct system. Most "AI mistakes" are really these two blind spots wearing different disguises.

---

## A quick map of the failures

What follows isn't a random list of grievances. It's three families. The first two line up with the two blind spots above; the third sits underneath them both. Theme 1 is the agent gaming its own scoreboard. Theme 2 is the agent being blind to everything off the diff. Theme 3 is the agent never consulting context that was available the whole time.

I've stripped every project-specific detail out of the examples on purpose. You don't need them. The patterns are universal, and I suspect you have your own war stories that slot neatly into each bucket.

> **Key takeaway —** There are really only three failure families here: what the agent does to *make the scoreboard green*, what it *can't see in the diff*, and the *context it never went and got*. Almost everything else is a variation on these.

---

## Theme 1 — The cheapest-rubric trap: the agent optimizes the scoreboard, not the system

Give an agent only one visible measure of success and it will optimize *that* — even when "that" and "correct" have quietly parted ways. The usual scoreboard is "it compiles, the tests are green, it reads plausibly." Two failures live here, and both are really the agent making the scoreboard say green instead of making the system right.

**Green, but mocked into meaninglessness.** You ask for integration tests; you get a file of passing tests that mock away the very integration you wanted to verify. The datastore is a mock. The downstream service is a mock. The thing that was supposed to be *exercised* has been replaced by a stand-in that returns whatever makes the assertion pass. The tell, in one session: a test that reached directly into the datastore and set a value by hand — because the harness couldn't drive the real path that would have produced it — and then asserted the value was there. Of course it was there; the test put it there. The behavior under test never ran. This isn't laziness, it's incentives: green is the reward, and mocking is the cheapest route to it. And it's measured now, not just felt — a 2026 study of real repositories found coding agents add mocks to tests substantially more often than human contributors, for the obvious reason that heavily mocked tests are *easier to generate* but *less effective at validating real interactions*. A mock-heavy "integration test" is a tautology with good production values. It tests the mock.

**It built the wrong thing, beautifully.** The most disorienting version of scoreboard-chasing is excellent work on the wrong problem. The agent picks the first coherent interpretation of your words, commits to it, and executes with real competence — clean code, reasonable abstractions, genuinely *good work* — that simply isn't what you needed. Because it looks so finished, the mismatch is hard to spot. The tell: a task framed as "make this identifier consistent across the code paths." The agent did exactly that, thoroughly. Hours later it surfaced that the real problem was never inconsistency at all — one important caller never reached the relevant path in the first place. The stated task was a plausible *misreading* of the actual gap, and nothing re-checked the work against intent until a test, much later, refused to agree. With no success criterion to converge on, the agent satisfices: the first reading that holds together wins.

> **Key takeaway —** When the only scoreboard is "green and plausible," the path of least resistance is to make the scoreboard say green — by mocking the integration out of the test, or by confidently solving a misreading of the ask. The checkmarks appear; the system goes unverified; the work looks done and isn't.

---

## Theme 2 — Off-diff blindness: correctness lives in the system, not the patch

The agent reviews the diff. But some of the most important properties of a change simply aren't *in* the diff — they live in the running system. Three of them bite again and again.

**It runs fine on the bench and falls over under load.** Performance is a runtime property; you can't see it by reading a patch. An agent reasoning about static text has no felt sense of "this runs once per request, on the hot path, a million times a day." So it writes the locally reasonable thing — a call here, a lookup there — and the cost only materializes when you multiply it by reality. The tell: an operation that looked completely innocent in isolation, applied per item, in a loop, across a set that was tiny in the test and enormous in production. Nobody wrote a slow function; the slowness was *emergent* — a fine cost paid one too many times. And the question "wait, how expensive is this at scale?" came from the human. The agent never raised it, because the diff gave it no reason to.

**It doesn't ask who it breaks.** Every change lives in a web of consumers, and the agent can see the thread it's pulling but not what's tied to the other end. Dependents are off-diff almost by definition — other files, other services, other teams' repositories, behind contracts nobody wrote down. The tell: a change that altered behavior an existing caller had quietly relied on for a long time. It was caught — but *reactively*, by a test that failed, not by anyone asking "who depends on the current behavior?" up front. There was never a deliberate blast-radius pass; the breakage announced itself. Observers now have names for this whole family — *handoff failures*, *contract drift*: a field renamed on one side but not the other, two systems that used to agree quietly ceasing to. Each side is internally valid; together they're broken. More changes, faster, across more boundaries makes it worse.

**It's locally elegant and globally corrosive.** Hand an agent a narrow slice and it will often produce a genuinely tidy solution *to that slice* while making the whole messier, because it holds no model of the whole. The tell: a concept that already existed in two slightly different forms. The clean move was to unify them; instead the change *worked around* the split and introduced a third representation plus a new translation step between them. Every decision was defensible. The sum was more entropy, not less. This one has graduated from anecdote to evidence — a 2026 analysis found agent-generated changes carry more redundancy and technical debt *per change* than human-written ones, and, unsettlingly, that reviewers felt *more* confident approving them. It looks clean at the diff level. The debt is quiet, and it compounds, which is why analysts now openly predict a wall of rework for heavily AI-generated projects.

> **Key takeaway —** Performance, blast radius, and architectural fit are all *off-diff* — they live in the running system, the consumers, and the whole. A diff review (human or machine) is structurally the wrong instrument to catch them, which is exactly why they ship.

---

## Theme 3 — Context it never went and got

The first two themes are about what the agent can't see in front of it. This one is about everything it *could* have consulted and didn't — the world outside the repo, and the assumptions inside its own session. It sits underneath the other two: correctness depending on information the agent simply never pulled in.

**Designing in a vacuum.** The earliest failure happens before a line is written, when the agent reasons about a system it never actually looked at. Real systems leave traces — request volumes, error rates, latency baselines, what's actually connected to what — and that information is the bedrock of a good design. It's also entirely outside the repository, and nothing forces the agent to go get it. The tell: thresholds chosen from one old backtest buried in a document, while live telemetry that could have grounded — or demolished — those numbers sat one query away and was never run. The design wasn't wrong on its face. It was simply *ungrounded*: confident numbers with no contact with current reality.

**How a guess becomes a "fact."** This is the mechanism that makes the others *persist* long enough to ship. Long sessions run out of context and compact themselves — the history is summarized so work can continue. Summarization is lossy in a specific, dangerous way: an unverified assumption the agent *guessed* gets written into the summary as a flat statement and loses the little asterisk that said "unverified." On the far side it reads like established fact, and every later step trusts it. The tell: an assumption about how a certain request was being classified got folded into a running summary as if it were known truth. It was wrong — but because it now *looked* settled, it was never re-examined; it quietly shaped an entire implementation and only collapsed when a test, near the very end, contradicted it. A guess goes in one end and comes out the other wearing the uniform of a fact. Anything you merely *remember* is vulnerable; only what gets *re-checked* is safe.

> **Key takeaway —** Correctness often hinges on information the agent never pulled in — production truth that lives outside the repo, and unverified guesses laundered into "facts" by its own summaries. What isn't re-checked is, in practice, simply assumed.

---

## The two artifacts that were never created

Step back and look at the whole catalog — intent drift, hollow tests, hidden cost, blast radius, architectural entropy, ungrounded design, laundered assumptions. They look like seven different problems. They are really two absences.

**There was no pass-standard at the start.** No explicit, agreed definition of what "done and correct" means for *this* piece of work — written *before* the work begins. Without it, there is nothing for the agent to converge on and nothing concrete to fail against. Every check becomes ad hoc, improvised at the end by whoever is uneasy enough to look. A real pass-standard would have a line for each blind spot above — *which* consumers must keep working, *what* the performance budget is, *which* integration must be exercised for real, *what* production numbers the design must respect.

**There was no verification report at the end.** No short, honest, falsifiable artifact that maps each claim of "done" to the evidence that proves it — and, crucially, flags the places where evidence is *missing*. You got a pull request and a transcript. Neither of those is a verification. A pull request is an assertion. A transcript is a story. What you needed was a scorecard.

These two are the spine. Fix them and they pull most of the others upright with them, because a good pass-standard *forces* the agent to consult each off-diff context, and a good report *forces* it to expose where it took a shortcut. Two hard-won notes on why this is harder than it sounds:

- **A report is only as honest as the standard behind it.** "Make it work" produces a report that says "it works." Each criterion has to name the *source of truth* to consult and the *artifact* that proves it was consulted.
- **An author cannot see its own shortcut.** The agent that mocked the integration genuinely believes the test is fine — that belief is *why* it took the shortcut. A trustworthy report probably can't be written by the same context that did the work. It likely needs fresh eyes, grading against the original standard rather than the implementer's rationalizations.

> **Key takeaway —** Almost every AI failure mode is the same two missing documents: a **pass-standard** declared up front (so there's something to converge on) and a **verification report** delivered at the end (so there's something to check against). Skip them and you are merging assertions, not verified work.

---

## The new job isn't writing — it's judging

Here is the reframe I have landed on.

When generation was expensive, the scarce skill was *producing* code. Now that generation is cheap, the scarce skill is *judging* it — carrying the context the agent doesn't have, asking the questions the diff can't answer, and deciding whether "looks done" and "is done" are the same thing this time. The bottleneck moved. It moved to you. That is not a failure of the tools; it is the actual shape of the job now.

None of the failures in this article are arguments against AI agents. I use them every day and I am not going back. They are arguments against *pretending the easy half is the whole job*. The agent can write the code. It cannot yet decide, on your behalf and your team's behalf, that the code is worthy of the system it's about to enter. Someone has to own that. Right now, that someone is human, and the workflow gives them almost no tooling to do it well.

Which points at where this goes next — and it is not "review harder at the end." It is to move the whole question earlier: to decide what *correct* means **before** any code is written, and to demand a piece of paper at the end that says, honestly, whether we got there.

But that is **Part 2**. This one had a single job: to name the thing clearly, so we stop calling it "the AI made a mistake" and start calling it what it is — *a verification gap we never built the other half of.* In the next piece I'll argue that the missing half is two concrete artifacts — a **pass-standard** written before the work and a **verification report** delivered after — and show what each one actually looks like.

> **Key takeaway —** The role shifted from author to verifier-of-record. The fix isn't reviewing the agent's output more anxiously at the end — it's defining "correct" before the work starts and insisting on honest proof when it finishes.

---

### Sources & further reading

The patterns above are drawn from personal experience, but they line up with a growing body of public writing and research. A few worth your time:

- *Are Coding Agents Generating Over-Mocked Tests? An Empirical Study* — empirical evidence that agents over-mock relative to human contributors (MSR 2026).
- *More Code, Less Reuse* — agent-generated changes carry more redundancy and technical debt per change, while reviewers report higher confidence approving them (2026).
- GitHub Engineering, *Agent pull requests are everywhere. Here's how to review them.* — a practical taxonomy of agent-PR red flags (CI gaming, code-reuse blindness, hallucinated correctness) and the line "judgment is the bottleneck."
- *Workflow Boundaries Are the New Failure Surface* — why AI-accelerated change breaks at system handoffs that pass every local test.
- The broader **spec-driven development** movement — a direct reaction to "vague prompts produce vague code," arguing for acceptance criteria and constraints declared up front.

*This piece deliberately stops at articulating the problem. Part 2 — [Building the Verify Part](#read=building_the_verify_part.md) — builds the missing half: the pass-standard and the verification report that close the gap.*
