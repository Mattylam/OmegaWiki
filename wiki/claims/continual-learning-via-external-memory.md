---
title: "Continual learning for LLM agents can be achieved via external memory evolution without parameter updates"
slug: continual-learning-via-external-memory
status: supported
confidence: 0.75
tags: [continual-learning, llm-agents, memory-based-learning, frozen-llm, deployment-time-learning]
domain: NLP
source_papers: [memento-skills-let-agents-design-agents, reasoningbank-scaling-agent-self-evolving-reasoning]
evidence:
  - source: memento-skills-let-agents-design-agents
    type: supports
    strength: strong
    detail: "Memento-Skills achieves +26.2% and +116.2% relative improvements on GAIA and HLE over 3 reflective-learning rounds with zero LLM parameter updates; skill library grows from 5 atomic skills to 41 (GAIA) / 235 (HLE); +13.7/+20.8 pp over static-library ablation."
  - source: reasoningbank-scaling-agent-self-evolving-reasoning
    type: supports
    strength: strong
    detail: "ReasoningBank achieves 48.8% WebArena success rate (vs ~28% no-memory baseline) and 34.2% relative gain over success-only baselines by iteratively distilling reasoning strategies from agent experience with frozen LLM; MaTTS further amplifies gains through test-time scaling."
conditions: "Requires (a) structured task clusters where skills can generalise, (b) reliable feedback signal from environment to drive the Write step, (c) capable retrieval policy that optimises for execution success rather than surface similarity. Empirically validated on closed-domain benchmarks with ground-truth answers; open question whether it generalises to preference-based domains without fixed ground truth."
date_proposed: 2026-04-13
date_updated: 2026-04-24
---

> [!abstract] Statement
> An LLM agent can achieve substantial, sustained performance improvements on held-out tasks by iteratively reading from and writing to an external memory of reusable skill artefacts (code, prompts, specifications), without modifying the underlying LLM's parameters. Adaptation is realised through memory evolution rather than weight evolution.

## Evidence summary

**Supporting evidence:**
- **[[memento-skills-let-agents-design-agents]]** (2026): formalises this as the Stateful Reflective Decision Process (SRDP); provides convergence guarantee (inherited from Memento 2, Thm. 8) for KL-regularised soft policy iteration over the Reflected MDP; demonstrates empirical gains of 26.2% (GAIA) and 116.2% (HLE) relative improvement across 3 rounds with frozen LLM; +13.7/+20.8 pp over static baseline.
- **[[reasoningbank-scaling-agent-self-evolving-reasoning]]** (2025, Google Research): introduces ReasoningBank — reasoning strategy memory distilled from both success and failure trajectories. Achieves 48.8% WebArena success (vs ~28% no-memory baseline) and 34.2% relative gain over success-only baselines with frozen LLM backbone; further amplified by memory-aware test-time scaling (MaTTS).

> [!info] Conditions and scope
> The claim holds under the following preconditions:
> - **Structured feedback signal**: the environment must return a reward or success indicator the Write operation can act on
> - **Retrieval policy quality**: a learned router (here: contrastive, behaviour-aligned) materially outperforms semantic-similarity retrieval
> - **Domain-structured skill space**: cross-task transfer is strongest when the skill library aligns with benchmark domain structure — generalisation across arbitrary tasks is not claimed
> - **Ground-truth availability**: evidence comes from closed-domain benchmarks (GAIA, HLE) with verifiable answers; unclear whether the approach extends to preference-based tasks where ground truth is contested or evolving

> [!warning] Counter-evidence
> None identified in the ingested literature. Open questions (see below) capture unverified conditions that could yield counter-evidence in future work.

## Linked ideas

None yet. This claim is relevant to the self-learning project's Phase 2 direction (automated prompt optimization from Phase 1 records) and should be considered when designing Phase 2 — Memento-Skills' skill-level memory evolution is a stronger generalisation of what DSPy-based prompt optimization achieves at the module level.

> [!question] Open questions
> - Does the approach generalise to preference-based domains without stable ground truth (e.g. underwriter feedback, where "correct" itself shifts)?
> - How does sample efficiency compare to parameter-efficient fine-tuning (LoRA) at matched compute?
> - Does the convergence guarantee still hold when Write mutates skill folders rather than only appending episodes (the assumptions from Memento 2 were for append-only memory)?
> - How does skill library quality evolve at 10× or 100× longer horizons?
