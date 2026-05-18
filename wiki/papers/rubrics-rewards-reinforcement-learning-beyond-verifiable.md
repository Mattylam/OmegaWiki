---
title: "Rubrics as Rewards: Reinforcement Learning Beyond Verifiable Domains"
slug: rubrics-rewards-reinforcement-learning-beyond-verifiable
arxiv: "2507.17746"
venue: "arXiv preprint"
year: 2025
tags: [reinforcement-learning, llm-as-judge, post-training, rubrics, rlhf, grpo]
importance: 3
date_added: 2026-04-27
source_type: pdf
s2_id: ""
keywords: [rubrics, reinforcement learning, RLVR, GRPO, LLM-as-judge, post-training, multi-criteria reward, healthcare, GPQA]
domain: "NLP"
code_url: ""
cited_by: []
---

## Problem

[[fine-tuning|RL post-training]] with verifiable rewards (RLVR) works well in domains where correctness is binary and machine-checkable (math, code), but breaks down in real-world expert tasks (medicine, science) where evaluation requires nuanced multi-criteria judgment. Standard alternatives — preference-based reward models or Likert-style [[llm-as-judge]] scoring — overfit superficial artifacts (length, formatting, annotator bias), require large pairwise comparison sets, and provide opaque, low-granularity signals.

## Key idea

Replace scalar Likert judges with **instance-specific checklist rubrics** as the on-policy RL reward signal. For each prompt, generate a rubric of 7–20 self-contained, weighted criteria (Essential / Important / Optional / Pitfall), then have an [[llm-as-judge]] score responses against the rubric. The aggregated rubric score becomes the reward in [[grpo]] training. This generalizes RLVR (which is the special case k=1 with a single verifiable correctness criterion).

## Method

**Rubric desiderata** (4 design principles):
- Grounded in expert guidance (use reference answers as expert proxies)
- Comprehensive coverage (factual, logical, completeness, style, safety, pitfalls)
- Criterion importance (categorical weights)
- Self-contained evaluation (each criterion judgeable in isolation)

**Rubric generation**: LLM (o3-mini, GPT-4o) synthesizes rubrics from reference answers. Released datasets: RaR-Medicine (~20k prompts), RaR-Science (~20k prompts).

**Two aggregation strategies**:
- *Explicit aggregation* (Eq. 1): each criterion judged independently as binary, weighted sum normalized. Hand-tuned weights — Essential: 1.0, Important: 0.7, Pitfall: 0.9, Optional: 0.3.
- *Implicit aggregation* (Eq. 2): all rubric items + categorical weights passed to a single LLM judge that outputs one scalar Likert score, normalized to [0,1]. Avoids hand-tuned weights.

**Training**: [[grpo]] on Qwen2.5-7B base policy, batch size 96, lr 5e-6, 10% linear warmup.

## Results

**HealthBench** (5k clinical conversations, physician-authored rubrics):
- Qwen2.5-7B base: 7.7%
- Qwen2.5-7B-Instruct: 22.7%
- Direct-Likert: 25.5%
- Reference-Likert: 28.9%
- RaR-Predefined (generic rubrics): 12.5%
- RaR-Explicit: 29.7%
- **RaR-Implicit: 31.2%** (+31% relative over Direct-Likert)

**GPQA-Diamond** (multiple-choice science, 10 runs):
- Qwen2.5-7B-Instruct: 35.0%
- Direct-Likert: 34.8%
- Reference-Likert: 36.5%
- **RaR-Implicit: 37.6%** (+7% relative over Direct-Likert)

**Key ablation findings**:
- Predefined generic rubrics (12.5%) catastrophically underperform instance-specific ones — task-specific synthesis is essential.
- Reference-grounded synthetic rubrics (35.9%) ≈ human-authored rubrics (34.8%) >> reference-free synthetic (32.0%) on HealthBench-1k.
- Stronger rubric-generator LLMs produce stronger downstream policies (GPT-4o > GPT-4o-mini > Qwen-7B-Instruct as rubric authors).
- Rubric supervision benefits *small judges* most: tightens alignment with human preferences across judge scales, reducing variance.
- Pitfall criteria provide minimal additional gain — synthetic pitfalls likely lack specificity to anticipate true failure modes.

> [!warning] Limitations
> - Rubrics generated from **reference answers** — quality is upper-bounded by reference quality, and the method is not fully reference-free in practice.
> - Tested only on Qwen2.5-7B base; scaling to larger policies and richer domains untested.
> - Synthetic pitfall criteria are weak — true negative criteria appear to need human domain expertise.
> - Explicit aggregation with hand-tuned categorical weights is brittle; the implicit variant works better but offers less controllability.
> - Only two domains evaluated (medicine, science); generalization to other open-ended domains (legal, creative writing, multi-turn dialogue) is untested.
> - Likert and rubric judges share the same underlying LLM-as-judge machinery, so [[llm-judges-over-reward-low-perplexity-outputs|known judge biases]] may still propagate through rubric-based rewards.

> [!question] Open questions
> - Can rubrics be generated *without* reference answers at quality matching reference-grounded ones — e.g. via deliberation, retrieval, or self-play?
> - How does RaR compare to process reward models (PRMs) on multi-step reasoning?
> - Does RaR reward hacking emerge at longer training horizons (the rubric becomes the proxy that gets gamed)?
> - What rubric-generation failure modes correlate with downstream policy regressions?

> [!tip] My take
> The reframing of RLVR as "k=1 single-criterion rubric" is conceptually clean and gives a principled bridge between verifiable and preference-based RL post-training. The interesting empirical finding is *not* that rubric-implicit beats Direct-Likert (predictable — more structured prompt to the judge) but that **structured rubric supervision compresses judge-scale variance**, making small judges nearly as effective as large ones. That alignment-stability result is the load-bearing contribution for production RLHF cost reduction.
>
> The weakness is the dependence on reference answers — RaR is closer to *distillation-from-reference-via-rubric* than truly open-ended RL. The reference-free ablation (32.0% vs 35.9%) shows the gap. Combined with the brittleness of synthetic pitfall criteria, this suggests rubrics are most useful when you already have a (possibly small) gold reference set — the method is gold-set-bootstrapping, not pure self-improvement.
>
> The connection to [[reflective-prompt-evolution|GEPA-style]] reflective optimization is worth probing: both convert opaque scalar feedback into structured natural-language criteria. RaR uses rubrics at *training time* as reward; GEPA uses textual feedback at *inference time* as gradient. A natural unification is rubric-generated criteria that update across training rollouts.

## Related

- [[grpo]] — RL algorithm used for policy optimization
- [[llm-as-judge]] — underlying judge mechanism
- [[rubrics-as-rewards]] — central concept introduced
- [[rubric-based-rewards-outperform-likert-rewards-non-verifiable-rl]] — main empirical claim
- [[fine-tuning]] — RL post-training is a fine-tuning paradigm
- [[rubric-based-pointwise-scoring-is-implicitly-multi-choice]] — related claim about rubric-style judging
- [[llm-judges-over-reward-low-perplexity-outputs]] — known judge bias that rubric supervision may partially mitigate
