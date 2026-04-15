---
title: "Position Bias (LLM-as-Judge)"
aliases: ["positional bias", "order bias", "position consistency", "ordering bias in llm judges"]
tags: [llm-as-judge, evaluation-reliability, bias]
maturity: active
key_papers: [judging-judges-systematic-study-position-bias, justice-prejudice-quantifying-biases-llm-judge, am-more-pointwise-pairwise-revealing-position]
first_introduced: "2024"
date_updated: 2026-04-15
related_concepts: []
---

## Definition

Position bias is the tendency of an LLM-as-judge to select a candidate answer based on its **position** within the prompt (first vs second, or index in a list) rather than on its content. In pairwise comparison, it manifests as a systematic preference for the first (primacy) or second (recency) candidate; in rubric-based scoring over multiple score options, it manifests as a preference for options at specific rubric positions.

> [!tip] Intuition
> Judges are prompted with inputs of the form "Candidate A: … / Candidate B: …". A well-calibrated judge should return identical verdicts whether A and B are swapped. In practice, judges do not: they carry a prior over position that only gets overridden when the quality gap between candidates is large. When candidates are near-ties, position effectively decides the verdict.
> *Source: LLM analysis*

## Formal notation

For a judge $J$ and a pair of candidates $(a, b)$, define:

- **Position Consistency** $\text{PC}(J, a, b) = \mathbb{1}[J(a, b) = J(b, a)]$ — does swapping order preserve the verdict?
- **Preference Fairness** measures whether disagreements (PC = 0) are symmetric across positions.
- **Repetition Stability** — does $J$ return the same verdict under independent sampling of the same prompt? This is not itself position bias but bounds how much of apparent position bias is reducible to sampling noise.

## Key variants

- **Pairwise position bias**: primacy or recency preference in two-candidate settings
- **List-wise position bias**: preference skewed toward specific ranks in k-candidate list settings
- **Rubric position bias**: when a judge selects a score from a multi-choice rubric, preference skewed toward rubric options in specific positions (distinct failure mode; see [[llm-as-judge]] pointwise rubric variants)

## Variants

- Judge-Level drivers: some judges are intrinsically more position-biased than others (architecture / training-data artefact)
- Candidate-Level drivers: position bias is amplified when candidates are of similar quality
- Task-Level drivers: some task types (e.g. open-ended writing) induce more position bias than others (e.g. math)
- **Rubric-option position bias** (see [[am-more-pointwise-pairwise-revealing-position]]): rubric-based pointwise scoring is implicitly multi-choice — the judge exhibits position bias over *score options* when they are enumerated as a list. Mitigated by [[balanced-permutation-aggregation]].

## Comparison

| Bias | Trigger | Mitigation direction |
|---|---|---|
| Position bias | Order of candidates in prompt | Balanced permutation aggregation; symmetric prompting |
| [[sycophancy]] | User/prompt assertions | Neutral-framing prompts; adversarial probes |
| Self-preference | Own output signature | Model-family diversification; blind anonymisation |

## When to use

Any evaluation design that uses pairwise or list-wise LLM judgments — including preference-data collection for RLHF, benchmark leaderboards (Chatbot Arena-style), and pairwise feedback signals for prompt optimisation — must report position-consistency statistics alongside headline agreement numbers.

> [!warning] Known limitations
> - Position consistency is a necessary but insufficient condition for reliability — a judge with PC = 1.0 can still be miscalibrated against human ground truth.
> - Mitigations that aggregate across permutations (e.g. balanced permutation) increase query cost linearly in candidate count.
> *Source: LLM analysis*

> [!question] Open problems
> - Predicting position bias ex ante for a (judge, task) pair without running the full experiment
> - Whether fine-tuning judges on position-symmetric supervision removes position bias or only masks it
> *Source: LLM analysis*

## Key papers

- [[judging-judges-systematic-study-position-bias]] — systematic 150k-instance study, introduces the three core metrics
- [[justice-prejudice-quantifying-biases-llm-judge]] — includes position as one of 12 categories in the CALM bias taxonomy
- [[am-more-pointwise-pairwise-revealing-position]] — extends position bias to rubric-based pointwise scoring; proposes [[balanced-permutation-aggregation]] as mitigation

> [!tip] My understanding
> For the [[golden-evaluator-calibration-baseline]] study, position consistency should be reported as a *prerequisite* metric before any correlation analysis: if PC is low, correlation metrics are confounded because half the judge's disagreements with UWs are artifacts of order rather than judgment.
