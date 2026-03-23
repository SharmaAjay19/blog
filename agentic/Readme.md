# Agentic — Writing Guide

**Publication:** [Agentic](https://SharmaAjay19.github.io/blog/agentic/)
**Accent color:** `#b8f050` — Acid green
**Body font:** Lora
**Tone:** Analytical

> Agentic publishes deep technical architecture analysis grounded in the author's direct experience building large-scale production agentic AI systems — the failure modes, pivots, and hard-won principles that don't make it into the documentation.

---

## Who Reads This

Senior engineers and architects who are building or evaluating production AI agent systems. They have shipped software. They have read the papers. They are skeptical of hype. They come here because the official documentation describes how systems are supposed to work, and they need to understand why they actually fail. They do not need explanations of what an LLM is. They need explanations of why their agent degrades after 40 turns, why their multi-agent system has a hard cliff at four handoffs, and what to do about it.

---

## What Gets Published Here

**In scope:**
- Context engineering: token budget management, attention architecture, what enters the context window and why
- Agentic architecture patterns: tool design, memory systems, orchestration topologies
- Multi-agent coordination: failure modes, coordination protocols, generalist vs. specialist tradeoffs
- Production reliability: the gap between benchmark performance and real-world reliability
- LLM behavior at scale: long-context degradation, positional effects, probabilistic vs. deterministic task routing
- Industry pattern convergence: when independent teams arrive at the same architectural conclusions

**Out of scope (never publish here):**
- Tutorial-level introductions to LLMs, transformers, or prompt engineering basics — the reader already knows this
- Product reviews or benchmark comparisons between specific models — this is not a leaderboard
- Opinion pieces on the future of AI without architectural substance — speculation without grounding is not published here
- Anything that could have been written without production systems experience — if the insight doesn't have a production scar attached, it belongs somewhere else

---

## Article Writing Process

Every article follows this four-step process in order. No steps are skipped.

### Step 1 — Source Extraction

If the article is grounded in a primary source — a production system the author built, a post-mortem, an engineering blog from a credible team — extract all explicit learnings from that source first.

For each insight extracted:
- State it in the source's own framing before reinterpreting
- Note whether it was presented as a principle, an observation, or an anecdote
- Flag which insights are specific to the source's context and which appear to generalize

Do not yet cite external research. Do not yet synthesize. Only extract.

### Step 2 — Deep Research

For each insight from Step 1, conduct structured research to find:

**Corroboration:** Have other teams independently reached the same conclusion? Search engineering blogs from Google, Anthropic, Meta, Manus, Cohere, and other organizations actively shipping agents. Note the specific framing difference — convergence from different starting points is stronger evidence than corroboration from similar systems.

**Academic grounding:** Search arXiv, NeurIPS, ICML, ICLR, ACL proceedings for empirical evidence. The most useful papers for this publication include:
- "Lost in the Middle" (Liu et al., 2024) — long-context degradation
- Gorilla / Berkeley Function Calling Leaderboard (Patil et al., 2024) — tool proliferation effects
- MAST study (Cemri et al., 2025) — multi-agent failure taxonomy
- ACE framework (Zhang et al., 2026) — context engineering as research discipline

When citing a paper, state the specific quantitative finding, not just the paper's general topic. "41–86.7% of multi-agent systems fail in production (MAST, 2025)" is useful. "There is research on multi-agent failures (MAST, 2025)" is not.

**Counter-evidence:** Actively search for cases where the insight does not hold, was contested, or required caveats. A principle that breaks in 20% of cases needs to acknowledge that. Stating the conditions under which something is true is more useful than stating it as a universal.

Minimum research bar: 3 independent sources beyond the primary, with at least 1 peer-reviewed paper for any empirical claim.

### Step 3 — Synthesis

Group the research findings by the underlying mechanism, not by surface similarity.

Ask for each group: what is the single architectural root cause that explains all the observations in this group? This is the principle. The surface observations are the evidence. The principle is what gets written as a section heading.

For every principle, explicitly answer:
- **Why does it work?** — The mechanical explanation. What property of transformers, of token processing, of attention, of probabilistic inference explains why this principle holds?
- **Why does it matter?** — The production consequence. What does a system that ignores this principle look like after six months in production? What fails, in what way, at what scale?

If you cannot answer both questions, the principle is not ready to be written.

### Step 4 — Write the Article

Follow the Article Writing Standards below.

---

## Article Writing Standards

### Voice and Register

Write as a principal engineer explaining hard-won lessons to a peer who is also a principal engineer. Not as a teacher. Not as a consultant selling a framework.

Assume the reader knows what a transformer is, what a context window is, what tool calling means, and what a multi-agent system looks like in practice. Do not explain these. Start from the failure mode, not the definition.

Use first person plural ("we discovered", "we built", "we observed") when drawing on the author's direct production experience. This is the production credibility that distinguishes this publication — use it. Use third person ("teams have found", "research shows") for general industry observations.

Make strong claims when the evidence supports them. Hedge precisely when it doesn't. "This pattern consistently fails at four handoffs" is better than "this pattern can sometimes fail" and better than "this pattern always fails at exactly four handoffs." Calibrate.

### Opening and Provenance

Every article grounded in specific production experience opens with a provenance note in the following format:

```
> **A note on provenance.** [What was built, at what scale, in what context.
> Link to the public source if available. 1–3 sentences maximum.]
> What follows is the distillation of [those failures / those pivots / those
> lessons] — cross-referenced against [research / independent teams] that
> arrived at the same conclusions through [different / independent] paths.
```

This note establishes trust before the first argument. It tells the reader: this is not speculation.

After the provenance note, the article opens with a scene-setting paragraph. This paragraph:
- Names the gap between the expected behavior and the observed behavior
- Describes who encounters this gap (the reader's context)
- Does not summarize the article's conclusions

Example of a good opening (from "The RAM Doesn't Lie"):
> *There is a reliable pattern in how teams building production AI agents describe their journey: they spend months chasing model upgrades, tuning prompts, and debating orchestration topologies. The offline metrics improve. The demos look impressive. And then the system hits production, and none of it translates.*

This opening names a pattern the reader has lived. It establishes stakes. It does not say "in this article, we will discuss six principles of context engineering."

### Structure

Article sections should be named after the phenomenon or failure mode, not after the principle. Readers navigate toward problems they recognize.

| Instead of | Use |
|---|---|
| "Wide Tools" | "The Tool Explosion Anti-Pattern" |
| "Context Management" | "The Context Problem, Precisely Stated" |
| "Code Execution Pattern" | "The LLM-as-Calculator Mistake" |
| "Coordination Issues" | "The Multi-Agent Coordination Tax" |

The pattern: name the anti-pattern or failure mode, then diagnose it, then prescribe the architectural fix.

### Prose Rules

**No bullet points in the body of articles.** Lists are permitted only in:
- The "Key Principles Reference" table at the end (use a markdown table, not bullets)
- The "Selected Research Citations" section
- Inline code or configuration examples

All architectural principles must be written as prose paragraphs, not bulleted lists. If you find yourself writing bullets, you are avoiding the work of explaining how the items relate to each other. Do that work.

**Blockquotes are for key insights only.** Format:

```markdown
> **Insight:** [Statement of the principle in one sentence, framed as an action or observation.]
```

Use 3–7 per article. Each blockquote should be a statement so clear and compact that an engineer could put it on a whiteboard and it would stand alone. Do not use blockquotes for asides, caveats, or extended explanations.

**Bold for genuine emphasis only.** Bold marks the phrase in a paragraph that carries the most weight. One or two per paragraph at most. Never bold a sentence. Never bold a heading (headings are already weighted by font).

**Analogies must be structural, not decorative.** When an analogy is used — "context is RAM", "progressive disclosure is virtual memory" — the comparison must hold across multiple dimensions: RAM is finite, addressed, managed, and depleted; so is context. The analogy earns its place by transferring understanding, not by being evocative.

**Numbers are evidence.** When research provides specific quantities ("41–86.7% of multi-agent systems fail", "15% longer task trajectories", "+10.6% on benchmarks"), use them. Vague claims ("most systems fail", "significantly longer") are weaker and signal that the research was not read carefully.

### Key Principles Reference Table

Every article ends with a reference table. Format:

```markdown
## Key Principles Reference

| Principle | What It Means | Why It Works |
|---|---|---|
| [Short name] | [One sentence] | [Mechanism + citation] |
```

This table is for engineers who will return to the article as a reference. It should be complete enough to stand alone. The "Why It Works" column must include the mechanical explanation and at least one citation.

### Citations Section

Every article ends with a "Selected Research Citations" section after the principles table. Format:

```markdown
## Selected Research Citations

- Author, A. et al. (Year). "Paper Title." *Venue*. (arXiv:XXXX.XXXXX)
- Author, B. (Year). "Blog Post Title." Organization Engineering Blog. URL if stable.
```

Only cite sources that are actually referenced in the article body. Do not pad the citations list.

### Closing Paragraph

The final paragraph must:
- Restate the article's central insight at a higher level of abstraction (not the specific mechanisms, but what they reveal about the nature of the system)
- End with a single declarative sentence that does not hedge, does not qualify, and does not ask the reader to "consider" anything

The last line of "The RAM Doesn't Lie":
> *The RAM doesn't lie. Fill it poorly, and the program crashes. Fill it deliberately, and the program runs.*

This is the target register: compact, confident, concrete.

---

## Tag Vocabulary

Only use tags from this list when adding articles to the manifest. To propose a new tag, add it here before using it.

| Tag | Use for |
|---|---|
| `Context Engineering` | Articles about what enters the context window, token budget management, attention budgets |
| `Architecture` | Structural patterns: how systems are assembled, orchestration topologies |
| `Multi-Agent` | Coordination, handoffs, orchestration, specialist vs. generalist tradeoffs |
| `Tool Design` | Tool catalogs, wide vs. narrow tools, tool selection accuracy |
| `Memory Systems` | Short-term, long-term, external state, history compaction |
| `Production AI` | Reliability, the gap between benchmarks and production, failure modes |
| `LLM Behavior` | Probabilistic nature of inference, positional effects, degradation patterns |
| `Code Execution` | Code interpreter patterns, Plan-then-Execute, deterministic delegation |

---

## Article File Naming

`{primary_topic}_{secondary_topic}.md` — all lowercase, underscores, no hyphens.

The primary topic should correspond to a tag. The secondary topic should be the specific angle or failure mode.

```
agentic_context_engineering.md     ✓
tool_design_wide_vs_narrow.md      ✓
the-multi-agent-problem.md         ✗  (uses hyphens)
MultiAgentFailures.md              ✗  (uses caps)
article1.md                        ✗  (not descriptive)
```

---

## Adding a New Article to the Publication

When an article `.md` file is complete and passes all quality gates:

1. Add an entry to the `ARTICLES` array in `agentic/index.html`:
   ```javascript
   {
     file:     'new_article_filename.md',
     title:    "Article Display Title",
     subtitle: 'One sentence that earns the click — under 15 words',
     date:     'YYYY-MM-DD',
     tags:     ['Tag One', 'Tag Two'],    // drawn from tag vocabulary above
     featured: true,
     readTime: 'N min read'
   }
   ```
2. Set `featured: false` on all previously featured articles
3. Update the article count in the hero meta row in `agentic/index.html`:
   ```html
   <span class="em">N&nbsp;articles</span>
   ```

---

## Quality Gates — Agentic-Specific

In addition to the platform-wide gates in the root README, every article in Agentic must pass:

- [ ] The article is grounded in at least one specific production system or real deployment (not solely theoretical)
- [ ] The provenance note is present if the article draws on the author's direct experience
- [ ] At least 3 research citations are present, each with a specific finding stated in the article body
- [ ] No section of the article body consists only of bullet points
- [ ] Every architectural principle explicitly answers "why it works" (mechanism) and "why it matters" (production consequence)
- [ ] The opening paragraph does not summarize the article's conclusions
- [ ] Blockquotes are used between 3 and 7 times — neither fewer nor more
- [ ] The closing paragraph ends with a single, unhedged declarative sentence
- [ ] The Key Principles Reference table is present with all three columns populated
- [ ] The Selected Research Citations section is present with full citation information
- [ ] All tags are drawn from the tag vocabulary above
- [ ] The article is a minimum of 1,500 words
- [ ] Section headings name the failure mode or phenomenon, not the principle

---

*This writing guide is the authoritative reference for all content published in Agentic. It is updated when new patterns emerge from the writing process. An AI agent writing for this publication must read this document in full before drafting.*
