---
title: "Rubrics as Rewards"
aliases: ["RaR", "rubric-based reward", "checklist-style reward", "rubric reward signal", "structured rubric reward"]
tags: [reinforcement-learning, llm-as-judge, post-training, rubrics, rlhf]
maturity: emerging
key_papers: [rubrics-rewards-reinforcement-learning-beyond-verifiable]
first_introduced: "2025"
date_updated: 2026-04-29
related_concepts: [llm-as-judge, grpo, llm-verifier, llm-judge-bias-taxonomy]
---

## Definition

**Rubrics as Rewards** (RaR) is a paradigm for on-policy reinforcement learning post-training of LLMs in which the scalar reward is computed from a model's response against a structured, instance-specific *rubric*: a checklist of weighted, self-contained, binary or scalar criteria. The rubric is judged by an [[llm-as-judge]], and the aggregated rubric score serves as the reward in policies trained via [[grpo]] (or analogous on-policy RL algorithms).

This generalizes RLVR (RL with Verifiable Rewards): RLVR is the special case of a single-criterion rubric where the lone criterion is a verifiable correctness check (exact match, test-case execution).

> [!tip] Intuition
> Replace a single opaque preference signal ("response A > response B" or "Likert score 7/10") with a structured checklist that says exactly *which dimensions* a response satisfies and *how much each dimension matters*. The judge's job becomes simpler (mark each item Pass/Fail) and the reward becomes interpretable (you can read off which criteria the policy is missing). This trades the universality of preference comparison for the granularity and auditability of explicit criteria.

## Formal notation

For prompt $x$ and sampled response $\hat{y} \sim \pi_\theta(\cdot|x)$, each prompt is associated with a rubric $\{(w_j, c_j)\}_{j=1}^k$ where $w_j \in \mathbb{R}$ is the weight of criterion $j$ and $c_j: (x, \hat{y}) \mapsto \{0,1\}$ is a binary correctness function.

**Explicit aggregation**:
$$r(x, \hat{y}) = \frac{\sum_{j=1}^k w_j \cdot c_j(x, \hat{y})}{\sum_{j=1}^k w_j}$$

**Implicit aggregation**: pass full rubric (criteria + categorical weights) to a single LLM judge that outputs one normalized scalar score, $r_{\text{implicit}}(x, \hat{y}) = f_\phi(x, \hat{y}, \{d_j\}_{j=1}^k)$.

## Variants

- **RaR-Predefined** — fixed generic rubrics applied uniformly across all prompts (e.g. "response is concise"). Empirically weak — generic criteria miss prompt-specific failure modes.
- **RaR-Explicit** — instance-specific rubrics, each criterion judged independently, weighted sum. Hand-tuned categorical weights (Essential 1.0 / Important 0.7 / Pitfall 0.9 / Optional 0.3).
- **RaR-Implicit** — instance-specific rubrics, single holistic LLM judge call delegates aggregation. Strongest empirical results in the originating paper.
- **Reference-grounded vs reference-free rubric synthesis** — rubrics generated with access to a gold reference answer significantly outperform rubrics generated without (35.9% vs 32.0% on HealthBench-1k).

## Comparison

| Reward type | Granularity | Interpretability | Annotation cost | Scope |
|---|---|---|---|---|
| Verifiable (RLVR) | binary | trivial | none | math, code |
| Preference / Bradley-Terry | scalar | low | high (pairs) | broad |
| Direct Likert | scalar | low | low | broad |
| Reference-Likert | scalar | medium | medium | needs reference |
| **Rubric (RaR)** | per-criterion | high | medium (rubric synthesis) | broad, expert domains |

## When to use

- Tasks where correctness is multi-dimensional (factual + safety + completeness + style) and a single scalar would discard most of the signal.
- Expert domains (medicine, law, science) where pairwise preference annotation at scale is cost-prohibitive but a small reference set or human-authored rubrics exist.
- When training with smaller LLM judges where Likert-only scoring is unreliable — rubric structure significantly improves small-judge alignment.

> [!warning] Known limitations
> - Rubric quality is bounded by reference-answer quality and rubric-generator capability. Reference-free rubric synthesis loses ~3-4 absolute points on HealthBench-1k.
> - Synthetic *pitfall* (negative) criteria are weak — they require anticipating common failure modes, which seems to require human domain expertise.
> - Hand-tuned categorical weights in explicit aggregation are brittle; implicit aggregation works better but offers less controllability.
> - Shares failure modes with [[llm-as-judge]]: judge biases (length, position, sycophancy, low-perplexity preference) can still propagate into the rubric reward.
> - Risk of reward hacking: at long training horizons the policy may game the rubric checklist rather than the underlying quality the rubric is meant to capture ([[goodharts-law]]).

> [!question] Open problems
> - Reference-free rubric generation that matches reference-grounded quality.
> - Rubric reward hacking detection and mitigation.
> - Composing rubric rewards with process reward models for multi-step reasoning.
> - Online rubric refinement during training (rubric updates as policy improves).
> - Generalization beyond medicine/science to creative, multi-turn, and agentic domains.

## Key papers

- [[rubrics-rewards-reinforcement-learning-beyond-verifiable]] — introduces RaR, releases RaR-Medicine and RaR-Science training sets, demonstrates +31% relative on HealthBench and +7% on GPQA-Diamond over Direct-Likert.

> [!tip] My understanding
> RaR is essentially *structured judging* applied to RL post-training rather than evaluation. Its real load-bearing finding is alignment-stability across judge scales — small judges with rubric scaffolding match larger judges with Likert scoring. That is the production-relevant result: it lets you swap a small open-weight judge for a large closed-weight one without paying an alignment penalty, *if* you have rubrics. The rubric synthesis cost (an o3-mini or GPT-4o call per training prompt) is a one-time pre-training expense, while the judging cost during RL rollouts can be paid by a small judge — a favorable cost shift.
>
> Conceptually adjacent to [[reflective-prompt-evolution]] (GEPA): both convert opaque scalar feedback into structured natural-language criteria. RaR operates at training time on rewards; GEPA at inference/optimization time on prompts. The natural unification is rubric criteria that *evolve* across training rollouts based on observed failure modes.
