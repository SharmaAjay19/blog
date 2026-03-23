# The RAM Doesn't Lie: Engineering Context as the Primary Lever for Reliable AI Agents

*How production deployments across the industry are converging on context architecture — not model quality — as the determinant of agentic system reliability*

*Ajay Sharma — Principal Software Engineer, Microsoft CoreAI*

---

> **A note on provenance.** The principles in this article are not theoretical. They were forged building <a href="https://learn.microsoft.com/en-us/azure/sre-agent/" target="blank_">Azure SRE Agent</a> — a production AI system that autonomously manages cloud infrastructure and handles incidents at scale. We started with 100+ tools, 50+ specialized sub-agents, and a prompt that read like a policy manual. We ended with 5 core tools and a handful of generalists. The agent got more reliable, not less. What follows is the distillation of those failures, pivots, and hard-won patterns — cross-referenced against research from across the industry that, strikingly, arrived at the same conclusions through entirely independent paths.

---

There is a reliable pattern in how teams building production AI agents describe their journey: they spend months chasing model upgrades, tuning prompts, and debating orchestration topologies. The offline metrics improve. The demos look impressive. And then the system hits production, and none of it translates.

The gap between benchmark performance and production reliability has become one of the defining engineering challenges of the agentic AI era. And across teams, domains, and tech stacks — from cloud operations to general-purpose automation to research agents — the same diagnosis keeps emerging: **the problem was never the model. The problem was what we put in front of it.**

In 2023, Andrej Karpathy offered an analogy that has since become foundational for practitioners: the context window is the agent's RAM. What followed from that framing, but took the industry another year and a half of production failures to fully internalize, is the corollary: **if the context window is RAM, then context engineering is memory management.** And you wouldn't ship production software without a memory management strategy.

This is the story of how that realization reshaped agent architecture across the industry — and the principles that emerged from the wreckage of brittle workflows, exploding tool catalogs, and cascading multi-agent failures.

---

## The Context Problem, Precisely Stated

Before examining solutions, it is worth being precise about the problem.

Large language models, regardless of their advertised context window size, do not treat all tokens equally. A landmark 2024 study in the *Transactions of the Association for Computational Linguistics* — "Lost in the Middle: How Language Models Use Long Contexts" (Liu et al.) — established empirically what many practitioners had suspected intuitively: model performance follows a **U-shaped curve relative to context position**. Information at the beginning and end of the context is reliably attended to; information in the middle degrades significantly in recall and utilization accuracy.

The architectural root cause is not a bug but a fundamental property of the transformer: attention computes pairwise relationships between every token and every other token, creating n² complexity for n tokens. As the context grows, the model's finite attention capacity is spread thinner across more relationships. Every token introduced depletes what Anthropic's engineering team has called an "attention budget."

This produces a critical insight for system designers: **adding tokens to the context is not a neutral act.** More tokens introduce cost in three dimensions simultaneously — latency (more computation per inference), monetary cost (billed by token), and accuracy (diluted attention precision). These costs are often invisible in development, where conversations are short and controlled, but compound in production, where agents run long-horizon tasks across diverse, unpredictable inputs.

The naive response — give the model a bigger context window — has proven insufficient. Research consistently shows that performance quality on long contexts lags behind performance on short ones, even for models explicitly trained for long-context use. More space to paste text is not an architecture. And yet, for most of the industry's early agent deployments, "paste more text" was exactly the strategy employed.

---

## The Tool Explosion Anti-Pattern

Every team that has built a production agent has encountered the tool explosion cycle. It proceeds with remarkable consistency:

A scope is defined. Tools are designed to match that scope. Users encounter edge cases outside the scope. A new tool is added. The new tool gets misused. Guardrails are added. The guardrails generate exceptions. More tools are added to handle the exceptions.

Within weeks of this cycle, a system that began with ten tools has acquired fifty. Within months, a hundred. Each new tool narrows the gap slightly but widens the system's surface area considerably. The agent becomes competent at the scenarios that have been explicitly encoded, and increasingly brittle everywhere else. At this point, the system is no longer an agent that reasons; it is a **workflow with a language model stapled to the front.**

Research confirms the model-level damage. The Gorilla paper (Patil et al., NeurIPS 2024), which introduced the Berkeley Function Calling Leaderboard as the standard benchmark for tool-use accuracy, demonstrated that LLM performance on API selection degrades as the tool catalog grows. Semantically overlapping tools increase the probability of wrong selection; the growing definition payload near the front of serialized context disrupts cache stability and reduces effective context for actual reasoning.

The production solution that has emerged across multiple teams is counterintuitive: **replace many narrow tools with a small number of wide ones**. A tool that exposes an entire ecosystem — a CLI environment, a programming language runtime, a general-purpose API surface — provides more capability than a hundred purpose-built wrappers, while consuming a fraction of the context budget. Critically, it also aligns with the model's own knowledge: LLMs trained on internet-scale data carry rich representations of widely-used interfaces in their weights. Abstracting those interfaces behind custom wrappers forces the model to work against its training priors.

The implication is a reorientation of tool design philosophy. Rather than asking "what specific actions should the agent be able to take?", the better question is: **"what interfaces does the model already understand, and how can we expose them directly?"** The answer is almost always fewer, broader, and more familiar than the initial design instinct suggests.

---

## The Multi-Agent Coordination Tax

Multi-agent architectures are perhaps the area where the gap between theoretical elegance and production reality is widest.

The motivating logic is sound: complex tasks can be decomposed into subtasks, each handled by a specialist agent. This mirrors how human organizations work — teams of experts with defined responsibilities, coordinating through handoffs. The implementation lesson is that **LLM-based agents are not reliable enough by default to be treated like human team members with shared context and professional judgment.**

A 2025 systematic analysis of production multi-agent failures (MAST study, Cemri et al.) examined over 150 failure traces and arrived at a stark quantification: **41 to 86.7 percent of production multi-agent systems fail** within hours of deployment. Nearly 79 percent of failures stem from specification and coordination issues — not from model capability limitations. Attempts to fix these with better prompts yielded modest improvements (+14% in one case study) that remained insufficient for deployment.

Four failure modes recur across systems. First, **discovery failures**: when one agent delegates to another, the routing graph often lacks visibility into the full capability tree. Reasonable requests reach dead ends — not because the capability doesn't exist, but because the path to it exceeds the orchestrator's horizon. Second, **system prompt fragility**: in a chain of agents with individual system prompts, one poorly calibrated agent contaminates the reasoning context for all downstream agents. Third, **infinite loops**: without strict termination logic, agents can bounce work between themselves indefinitely, burning tokens and latency without progress. Fourth, and most subtly, **tunnel vision**: agents with hard domain boundaries develop over-commitment to their domain's explanations, preventing cross-domain root-cause analysis that any generalist would find obvious.

Production evidence has consistently shown that problems requiring more than four handoffs in a chain approach near-zero completion rates. This is not a coincidence — it reflects the compounding probability of each handoff introducing context pollution, misalignment, or routing failure.

The emerging architecture that addresses these failures is not a return to single-agent systems but a shift in how multi-agent coordination is conceived. **Coordination must be treated as a distributed systems problem**, with the same rigor applied to message schemas, failure isolation, observability, and circuit breakers that would be applied to any microservices architecture. Structured communication protocols, explicit responsibility matrices, and real-time coordination monitors are engineering necessities, not optional enhancements.

For many use cases, the better architectural choice is fewer generalist agents equipped with wide tools, with domain knowledge loaded on demand rather than baked into specialized system prompts. This preserves the benefits of specialization — expert knowledge applied to specific problems — while eliminating the routing complexity of a large specialist network.

---

## The LLM-as-Calculator Mistake

There is a category of task that teams routinely misroute to language models: structured data analysis. Anomaly detection on time-series metrics. Column filtering on database outputs. Statistical aggregation across log entries. These tasks share a characteristic — they are deterministic, structured, and analytically tractable — and they are consistently handled worse by LLMs than by a few lines of code.

The misrouting is understandable. If the agent's tool for working with data is "put it in context and ask the model," then every data task becomes a language model task. The system "kind of works" for small datasets and simple queries — just enough to clear the demo threshold. But it fails in three ways: it is expensive (tokens proportional to data size), it is inaccurate (models struggle with zero-valued time series, sparse distributions, and numerical edge cases), and it scales poorly (tool failure rates increase with data complexity).

The correct architecture separates what language models do well from what deterministic code does well. **LLMs are decision engines, not compute engines.** Their role is to determine *what* analysis should be performed, in what sequence, with what parameters. Execution belongs to code — a Python interpreter, a SQL engine, a statistical library — that returns structured results back to the model.

This separation, sometimes called the "code interpreter" pattern, has been independently validated across production deployments and formalized in Plan-then-Execute architectures documented in recent agent design research. The planning model generates a structured execution sequence once; a cheaper executor carries out the steps; results feed back into the planner's reasoning. The expensive planning model is invoked sparingly; the bulk of task execution happens in deterministic code.

The benefits compound. Code execution produces exact results, not probabilistic approximations. Results can be verified automatically. The context footprint of the computation is bounded — a structured result object rather than raw data. And critically, code is inspectable and debuggable in ways that model internals are not, which makes failures traceable rather than mysterious.

---

## Explicit State vs. Implicit Accumulation

Consider two agents running a 50-step task. The first maintains a growing conversation history — every prior turn, tool call, and intermediate result accumulates in context. The second maintains an explicit todo list, updated after each step, and a structured summary of key facts discovered so far; history is periodically compacted into this summary rather than accumulated verbatim.

At step 5, both agents perform comparably. At step 30, the difference is substantial. The first agent's context has ballooned; the "lost in the middle" effect means that important context from early steps is being attended to less reliably. The model is spending attention budget processing irrelevant, stale, or redundant history. The second agent's context has stayed bounded; every token present is earning its position.

This is the principle behind two patterns that appear independently in every mature agent deployment: **explicit plan externalization** and **history compaction**.

Explicit plan externalization stores the agent's current task plan as a structured artifact outside the model's context — a todo list, a step sequence, a goal hierarchy. The model updates this artifact rather than reconstructing the plan on every turn from context. This eliminates a long-range dependency: the model's plan no longer depends on its ability to attend correctly to turn 3's planning discussion while processing turn 47's tool output.

History compaction continuously converts verbose conversation history into structured summaries. Rather than "Thought: I should check the deployment logs. Action: get_logs(). Observation: [wall of text]", the compacted representation might read "Deployment logs inspected: quota error found at 14:32 UTC. Subscription limits queried: current allocation 80%, requesting increase." The information is preserved; the token overhead is eliminated.

Research on context management from the JetBrains Research team (NeurIPS 2025) introduced a nuance worth noting: LLM-generated summaries, if miscalibrated, can *mask* signals that indicate the agent should stop pursuing a dead end, leading to 15% longer task trajectories without improved solve rates. Compaction must preserve task-relevant signals, not just reduce token count — it is an engineering problem, not a trivially automatic one.

The ACE research framework (Zhang et al., 2026) formalized this as a general principle for self-improving agents: contexts should function as **evolving playbooks** — structured, incremental, and rich with domain knowledge — rather than concise summaries that bleed away task-specific detail. Brevity bias, which drops insights in favor of shorter representations, is as dangerous as context bloat.

---

## Progressive Disclosure: Converting Unbounded Sources Into Bounded Interactions

Every production agent eventually encounters what might be called the adversarial tool call: a query to a data source that, executed naively, returns results whose token footprint exceeds the remaining context budget for the session. A database table with thousands of columns, a log file covering weeks of events, an API response that includes full document bodies when only metadata was needed.

The naive architecture has no defense against this. The tool call fires; the result arrives; the context window is consumed; the session fails.

The mature architecture treats large tool outputs as **data sources rather than context payloads**. Instead of injecting the result directly into the model's context, the system writes it to a sandboxed session environment and gives the model structured operations to interact with that session: inspect the schema, filter by criteria, analyze via code, and surface a bounded summary back to context. The model never sees 200,000 raw tokens; it sees a structured handle and a set of tools for exploring it.

This pattern — session-based progressive disclosure — is the agent equivalent of virtual memory: the working set in active context is small and high-signal, while the full data exists in a separate store that the agent can page into context selectively. The model's attention budget is spent on reasoning about the data, not processing it.

The architectural principle generalizes: **data delivery size should be determined by the agent's informational need, not by the tool's output cardinality.** Implementing this requires thinking about the data layer and the context layer as separate systems, with an explicit interface between them — a design discipline that most early agent implementations skip, to their eventual cost.

---

## The Convergence Signal

Perhaps the most significant finding from surveying production deployments and research across the industry is not any individual principle but their **convergence from independent starting points**.

Teams at a major cloud provider, a Chinese AI startup, Google's infrastructure team, and Anthropic's engineering organization all independently traversed similar journeys — from narrow tools to wide ones, from specialist agents to generalists, from prompt-centric design to context-centric architecture. They arrived at variations of the same principles without collaborating. The convergence is a signal: these are not team-specific heuristics or domain-specific tricks. They are fundamental properties of how language models interact with the information presented to them.

This convergence also illuminates what reliable agent architecture is optimizing for. It is not maximizing model capability utilization — it is minimizing the friction between the model's learned representations and the information environment it operates in. Wide tools work because they align with learned priors. Context compaction works because it reduces interference from irrelevant signals. Code delegation works because it routes computation to the substrate that handles it deterministically. Explicit state works because it removes long-range context dependencies.

Each principle, examined closely, is a specific manifestation of the same underlying insight: **the model's reliability is a function of the quality of what enters its context, not just the quality of the model itself.**

---

## Conclusion: What Context Engineering Is, and Why It's Not Optional

Context engineering is the practice of deliberately designing the information environment presented to a language model at every inference step — what is included, what is excluded, what form it takes, when it arrives, and how much of the attention budget each component is permitted to consume.

It is not prompt engineering, though it subsumes it. It is not RAG, though RAG is one of its tools. It is not orchestration, though orchestration decisions shape what context is generated. It is the discipline that operates above all of these — the systems-level practice of treating context as a first-class architectural component with its own lifecycle, constraints, and engineering requirements.

The industry's trajectory is clear. Model capability will continue to improve, and capable models will continue to raise the ceiling on what agents can accomplish. But the floor — the reliability threshold below which agents fail in production regardless of model quality — is set by context architecture. Teams that treat context as an afterthought will keep discovering, at every scale inflection point, that their architecture doesn't transfer. Teams that engineer context from the beginning will find that the same model, given a better information environment, performs reliably where others fail.

The RAM doesn't lie. Fill it poorly, and the program crashes. Fill it deliberately, and the program runs.

---

## Key Principles Reference

| Principle | What It Means | Why It Works |
|---|---|---|
| Context is a Finite Resource | Every token depletes an attention budget | Transformer n² attention; U-shaped positional performance (Liu et al., 2024) |
| Wide Tools Beat Narrow Tools | Expose broad interfaces the model already knows | Aligns with model priors; reduces context footprint; Gorilla degradation with catalog growth (Patil et al., 2024) |
| LLMs Are Decision Engines | Delegate deterministic computation to code | LLMs are probabilistic; code is exact; reduces token overhead |
| Coordinate Like a Distributed System | Multi-agent coordination requires contracts and observability | 41–86.7% MAS failure rate; 79% from coordination failures (MAST, 2025) |
| Explicit State Beats Implicit History | Externalize plans; compact history | Removes long-range context dependencies; prevents lost-in-the-middle degradation |
| Progressive Disclosure for Data | Write large payloads to sandboxed sessions | Prevents adversarial tool calls from consuming the context window |
| Trust Model Priors | Don't abstract over what the model already knows | Abstractions are taxes; leveraging priors is free capability |

---

## Selected Research Citations

- Liu, N.F. et al. (2024). "Lost in the Middle: How Language Models Use Long Contexts." *Transactions of the Association for Computational Linguistics*, 12, 157–173. (arXiv:2307.03172)
- Patil, S.G. et al. (2024). "Gorilla: Large Language Model Connected with Massive APIs." *NeurIPS 2024*. (arXiv:2305.15334)
- Zhang, Q. et al. (2026). "Agentic Context Engineering: Evolving Contexts for Self-Improving Language Models." *arXiv:2510.04618*
- Cemri, M. et al. (2025). "Why Do Multi-Agent LLM Systems Fail?" *arXiv:2503.13657*
- Anthropic Engineering. (2025). "Effective Context Engineering for AI Agents." anthropic.com/engineering
- Google Developers Blog. (2025). "Architecting Efficient Context-Aware Multi-Agent Frameworks for Production." developers.googleblog.com
- JetBrains Research. (2025). "Cutting Through the Noise: Smarter Context Management for LLM-Powered Agents." Presented at NeurIPS 2025 Deep Learning for Code Workshop.
- arXiv:2505.21298. (2025). "Large Language Models Miss the Multi-Agent Mark."
- arXiv:2509.08646. (2025). "Architecting Resilient LLM Agents."
- arXiv:2505.19591. (2025). "Multi-Agent Collaboration via Evolving Orchestration."

---

*This article synthesizes production experience building Azure SRE Agent with current research in agentic AI architecture. The principles described reflect patterns observed across multiple independent teams and validated by both empirical production data and peer-reviewed research. The field continues to evolve rapidly; readers are encouraged to treat these as durable architectural heuristics rather than fixed prescriptions.*
