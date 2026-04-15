---
title: "LLM judges exhibit measurable, non-random position bias driven primarily by candidate quality gap"
slug: llm-judges-exhibit-measurable-position-bias
status: supported
confidence: 0.8
tags: [llm-as-judge, position-bias, evaluation-reliability]
domain: NLP
source_papers: [judging-judges-systematic-study-position-bias]
evidence:
  - source: judging-judges-systematic-study-position-bias
    type: supports
    strength: strong
    detail: "150,000+ evaluation instances across 15 judges × 22 tasks demonstrate position bias is reproducible, varies systematically across judges/tasks, and is strongly correlated with inter-candidate quality gap (not prompt length)."
conditions: "Holds for pairwise and list-wise comparison settings on general-purpose benchmarks (MTBench, DevBench). Transfer to pointwise rubric-based scoring and to domain-specific high-stakes judgment (e.g. underwriting) is untested."
date_proposed: 2026-04-15
date_updated: 2026-04-15
---

> [!abstract] Statement
> When an LLM-as-judge is used in pairwise or list-wise comparison, its verdict is measurably influenced by the position of candidates in the prompt. This bias is not sampling noise: it is reproducible under repetition, varies systematically across judges and tasks, and is strongly amplified when candidates are of similar quality. Prompt-component length has only weak influence; candidate-quality gap is the dominant driver.

## Evidence summary

- **Large-scale**: 15 judges × 22 tasks × ~40 solution generators, >150k evaluation instances
- **Metrics**: repetition stability, position consistency, preference fairness — each independently measured
- **Decomposition**: Judge-Level, Candidate-Level, Task-Level factors identified as systematic drivers

> [!info] Conditions and scope
> - Applies to pairwise / list-wise LLM judgment
> - Benchmark coverage: MTBench, DevBench (general-purpose); not validated on domain-specific evaluations
> - The "quality-gap dominance" finding means the bias is maximally problematic when candidates are near-ties — exactly the regime where a good judge matters most

> [!warning] Counter-evidence
> None catalogued yet. Future ingests should check whether mitigation-focused papers (balanced permutation, de-biasing prompts) report residual position bias under their proposed methods.

## Linked ideas

- [[golden-evaluator-calibration-baseline]] — position consistency should be a prerequisite diagnostic in the GE calibration card

> [!question] Open questions
> - Does position bias measured on MTBench predict position bias on a custom, domain-specific calibration set?
> - Is the "quality-gap drives bias" relationship monotonic, or does it plateau at very narrow gaps?
