---
title: "ReasoningBank: Scaling Agent Self-Evolving with Reasoning Memory"
slug: reasoningbank-scaling-agent-self-evolving-reasoning
arxiv: "2509.25140"
venue: "arXiv.org"
year: 2025
tags: [llm-agents, agent-memory, self-evolution, failure-learning, test-time-scaling, experience-distillation, continual-learning, web-navigation, software-engineering]
importance: 4
date_added: 2026-04-24
source_type: pdf
s2_id: "9c03c7b9ae5bc182134edd736c6d48946f4aaaaf"
keywords: [reasoning memory, agent self-evolution, failure-driven learning, test-time scaling, experience distillation]
domain: NLP
code_url: "https://github.com/google-research/reasoning-bank"
cited_by: []
---

## Problem

LLM agents deployed in persistent real-world roles encounter continuous task streams but fail to learn from accumulated interaction history. Existing memory mechanisms either store raw trajectories (episodic memory) or retain only successful task routines — both representations waste the informational value of failures and lack the generalizability to transfer across tasks. The result: agents repeat past errors and cannot improve beyond their initialization.

## Key idea

**ReasoningBank** distills generalizable reasoning strategies from an agent's self-judged successful and failed experiences into a structured memory of titled, described reasoning principles. Unlike raw trajectory logs or success-only skill libraries, ReasoningBank explicitly mines both failure modes and success patterns, abstracting them into transferable, high-level strategies that persist across tasks.

At test time, the agent retrieves relevant reasoning memories from ReasoningBank to guide its interaction; after task completion, it integrates new learnings back — creating a closed-loop self-evolution cycle without modifying LLM parameters.

**Memory-aware Test-Time Scaling (MaTTS)** amplifies this by allocating more compute per task to generate abundant, diverse exploration trajectories. This produces richer contrastive signals (more failure/success pairs) for distilling higher-quality memories. Crucially, higher-quality memories then guide more effective scaling — a synergistic feedback loop between memory quality and test-time compute.

## Method

**Memory structure**: Each ReasoningBank entry is a structured item with: (1) a title (short label), (2) a description (when to apply the strategy), and (3) content (the reasoning principle itself). This is more abstract than Memento-Skills' executable code folders and more actionable than raw trajectory logs.

**Write operation (memory synthesis)**:
1. Agent attempts a task, generating a trajectory
2. LLM-as-a-judge self-evaluates the trajectory (success/failure)
3. For successful trajectories: distill high-level reasoning strategies that drove the success
4. For failed trajectories: identify failure causes and extract corrective reasoning principles
5. New memories are added to / merged into ReasoningBank

**Retrieval**: Embedding-based retrieval finds relevant memories given the current task context; retrieved memories are prepended as reasoning guidance in the agent's prompt.

**MaTTS**: For each task, generate $k$ diverse exploration trajectories (via temperature sampling) rather than a single rollout. The $k$ trajectories provide richer contrastive signals — both successful strategies and failure modes — enabling higher-quality memory synthesis. The resulting better memory guides more effective scaling in subsequent rounds.

**Benchmarks**: WebArena (web browsing, 812 tasks) and SWE-bench (software engineering); tested with multiple LLM backbones.

## Results

- ReasoningBank achieves **48.8% success rate on WebArena** with only 8.3 average steps, outperforming all baselines by significant margins
- **34.2% relative performance gain** over the next best memory mechanism (Synapse, AWM)
- **16% reduction in interaction steps** on successful instances (more efficient exploration)
- MaTTS further amplifies gains by generating diverse contrastive experience
- Emergent complex reasoning strategies appear over time not explicitly programmed
- Consistent gains across both web browsing and software engineering domains and across LLM backbones

Key comparisons:
| Method | Memory type | WebArena success |
|---|---|---|
| No memory | None | ~28% |
| Raw trajectory (episodic) | Full traces | ~37% |
| AWM / Synapse | Success-only routines | ~40% |
| **ReasoningBank** | Distilled strategies (success + failure) | **48.8%** |

> [!warning] Known limitations
> - Memory synthesis relies on LLM-as-a-judge for correctness signals — susceptible to judge errors and reward hacking
> - Memory structure is flat (titled entries); no hierarchical or compositional memory architecture explored
> - Retrieval uses simple embedding similarity — complex relational structures between memories are not exploited
> - MaTTS scales test-time compute linearly with $k$; diminishing returns not characterized beyond moderate $k$
> - Evaluated on closed-domain benchmarks with verifiable outcomes; unclear how well the approach transfers to preference-based or open-ended tasks

> [!question] Open questions
> - How does memory quality evolve at 10× or 100× longer deployment horizons?
> - Can ReasoningBank memories be shared across agents or task distributions (multi-tenant deployment)?
> - How does performance compare to parameter-efficient fine-tuning (LoRA) at matched compute?
> - Does failure-driven distillation still help when the LLM judge is weak or when ground truth is contested?
> - What is the optimal memory consolidation schedule (when to merge, prune, or deprecate old entries)?

> [!tip] My take
> The core contribution is conceptually clean: the information in failure traces is being thrown away by all prior work. ReasoningBank is essentially an offline RL signal being converted into a natural language policy — the "policy" being the set of reasoning strategies in the bank. MaTTS is a natural corollary: if memory improves with richer contrastive signal, generate more by throwing compute at it. The synergy between memory quality and test-time scaling is real and interesting.
>
> The key open question for deployment is whether LLM-as-a-judge reliability is sufficient — the whole system hinges on the agent correctly attributing success/failure to specific reasoning choices. Noisy judge signals could corrupt the memory bank over time (analogous to reward hacking in RL).
>
> *Source: LLM analysis + DeepXiv TLDR*

## Related

- [[skill-memory]] — predecessor paradigm storing executable code/prompts; ReasoningBank instead stores abstract reasoning principles; complements via different memory granularity
- [[agent-reasoning-memory]] — concept page for the ReasoningBank memory framework
- [[continual-learning-via-external-memory]] — supports this claim: frozen-LLM agents can self-improve via external memory evolution
- [[memento-skills-let-agents-design-agents]] — closely related predecessor; Memento-Skills uses executable skill folders, ReasoningBank uses reasoning strategy principles
- [[react-prompting]] — ReasoningBank agents operate via ReAct-style reasoning-action loops
- [[retrieval-augmented-generation]] — memory retrieval at test time is analogous to RAG; reasoning strategies replace documents
- supports: [[distilling-reasoning-strategies-failed-successful-agent]]
- supports: [[continual-learning-via-external-memory]]
