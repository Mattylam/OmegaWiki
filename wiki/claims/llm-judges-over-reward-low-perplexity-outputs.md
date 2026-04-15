---
title: "LLM judges over-reward low-perplexity outputs relative to human evaluators, independent of authorship"
slug: llm-judges-over-reward-low-perplexity-outputs
status: supported
confidence: 0.75
tags: [llm-as-judge, self-preference-bias, perplexity, evaluation-reliability]
domain: NLP
source_papers: [self-preference-bias-llm-judge]
evidence:
  - source: self-preference-bias-llm-judge
    type: supports
    strength: strong
    detail: "GPT-4 judges assign significantly higher scores than human evaluators to low-perplexity outputs, and the effect persists after controlling for self-authorship — implicating familiarity / perplexity as the underlying mechanism rather than authorship recognition."
conditions: "Demonstrated on GPT-4 in dialogue-system evaluation. Generalisation to other model families, non-dialogue tasks, and RLHF'd judges is not yet established. Requires judge logprobs for direct perplexity measurement."
date_proposed: 2026-04-15
date_updated: 2026-04-15
---

> [!abstract] Statement
> When an LLM-as-judge scores candidate outputs, its scores are systematically inflated for outputs that have low perplexity under the judge's own distribution, relative to scores assigned by human evaluators on the same outputs. This effect is observed whether or not the outputs are self-generated, indicating the bias is driven by distributional familiarity rather than authorship recognition.

## Evidence summary

- GPT-4 exhibits significant self-preference bias under a novel quantitative metric.
- The bias persists for non-self-generated low-perplexity outputs, ruling out authorship-recognition as a sufficient explanation.
- Judge-human score gap scales with output perplexity, suggesting a continuous familiarity effect rather than a discrete "is this mine?" classifier.

> [!info] Conditions and scope
> - GPT-4 in dialogue evaluation
> - Requires access to judge logprobs for mechanistic measurement
> - Has operational implications: if judge and generator share a base model, their perplexity distributions overlap heavily, amplifying the bias

> [!warning] Counter-evidence
> Not yet catalogued. Potential counter-evidence would be: (a) a judge from a different family showing the same perplexity-score correlation (would generalise the claim); (b) a judge where the correlation vanishes after perplexity-adjustment (would support a post-hoc de-biasing remedy); (c) RLHF'd judges where the bias shifts toward RLHF-preferred styles rather than raw low-perplexity text.

## Linked ideas

- [[golden-evaluator-calibration-baseline]] — motivates model-family diversification as a GE design constraint

> [!question] Open questions
> - Does the effect hold for open-weight judges (Llama, Qwen) as strongly as for GPT-4?
> - Is perplexity-adjustment an effective post-hoc de-biasing technique, or does it over-correct and damage discrimination?
