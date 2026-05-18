---
title: "Distilling generalizable reasoning strategies from both failed and successful agent experiences outperforms success-only or raw trajectory memory"
slug: distilling-reasoning-strategies-failed-successful-agent
status: weakly_supported
confidence: 0.7
tags: [agent-memory, failure-learning, self-evolution, continual-learning, llm-agents, experience-distillation]
domain: NLP
source_papers: [reasoningbank-scaling-agent-self-evolving-reasoning]
evidence:
  - source: reasoningbank-scaling-agent-self-evolving-reasoning
    type: supports
    strength: strong
    detail: "ReasoningBank (failure+success distillation) achieves 48.8% WebArena success rate vs ~40% for success-only baselines (AWM, Synapse) and ~37% for raw trajectory episodic memory; 34.2% relative improvement; gains hold across LLM backbones and domains."
conditions: "Demonstrated on closed-domain benchmarks with verifiable outcomes (WebArena, SWE-bench). Requires (a) reliable LLM-as-a-judge for success/failure attribution, (b) sufficient trajectory volume for distillation to produce generalizable principles, (c) tasks with structured failure modes rather than open-ended preference tasks."
date_proposed: 2026-04-24
date_updated: 2026-04-24
---

> [!abstract] Statement
> An LLM agent that distills generalizable reasoning strategies from BOTH successful and failed task experiences — rather than storing raw trajectories or retaining only successful routines — achieves meaningfully higher task success rates and better generalisation to unseen tasks. The failure signal provides corrective contrastive information that success-only memory systematically discards.

## Evidence summary

**Supporting evidence:**
- **[[reasoningbank-scaling-agent-self-evolving-reasoning]]** (2025, Google Research): ReasoningBank synthesises structured reasoning memory items from both success and failure trajectories on WebArena (812 tasks) and SWE-bench. Achieves 48.8% success rate vs ~40% for AWM/Synapse (success-only) and ~37% for episodic (raw trajectory) baselines. 34.2% relative improvement. Gains hold across multiple LLM backbones.

> [!info] Conditions and scope
> - **LLM judge quality**: the failure/success distinction depends on self-evaluation reliability; weak judges reduce the quality of distilled principles
> - **Task verifiability**: evidence comes from tasks with ground-truth outcomes; preference-based or open-ended tasks lack the clear failure signal that drives distillation
> - **Trajectory volume**: distillation quality improves with more experience — sparse task streams may not provide sufficient contrastive signal
> - **Domain**: demonstrated on web browsing and software engineering; other interactive agent domains are untested

> [!warning] Counter-evidence
> None identified yet. The ablation in ReasoningBank (success-only vs full) is within the same system; external replication across different benchmarks is lacking.

## Linked ideas

None yet.

> [!question] Open questions
> - Does the failure-learning advantage persist in domains where failure attribution is ambiguous (multi-step tasks where the error locus is unclear)?
> - How does the performance gap between failure+success and success-only memory evolve as the number of tasks scales?
> - Is the advantage from failure distillation or from the structured abstraction (titled principles) — would structured success-only memory close the gap?
> - Can failure-distilled strategies transfer to genuinely novel domains not seen during the agent's deployment?
