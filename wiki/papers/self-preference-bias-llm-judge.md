---
title: "Self-Preference Bias in LLM-as-a-Judge"
slug: self-preference-bias-llm-judge
arxiv: "2410.21819"
venue: "arXiv"
year: 2024
tags: [llm-as-judge, self-preference-bias, perplexity, evaluation-reliability]
importance: 4
date_added: 2026-04-15
source_type: pdf
s2_id: "cf01d7c40cbf815de0f62fa78c2352ba546ad680"
keywords: [self-preference bias, LLM-as-judge, perplexity, familiarity, GPT-4]
domain: NLP
code_url: ""
cited_by: []
---

## Problem

LLM judges are suspected to rate their own outputs higher than outputs from other models ("self-preference bias"), introducing a confound when a judge model is used to evaluate systems that include itself or a close sibling. Prior work lacked a quantitative metric for this bias and did not identify its mechanism.

## Key idea

Self-preference is a **symptom**, not the disease. The underlying mechanism is **familiarity**: LLM judges systematically over-reward outputs with lower perplexity under their own distribution, whether or not they authored those outputs. Self-generated text scores higher simply because it is, on average, lower-perplexity under the generating model.

## Method

- Introduce a quantitative self-preference metric comparing judge scores on self-generated vs other-generated outputs, controlling for human quality ratings.
- Ablate the self-authorship signal by measuring the relationship between **perplexity** (under the judge model) and **judge score** — across outputs regardless of origin.
- Compare human evaluator scores against LLM judge scores on the same outputs to isolate the bias term.

## Results

- GPT-4 exhibits a statistically significant self-preference bias under the proposed metric.
- **LLM judges assign higher scores to low-perplexity outputs than human evaluators do, regardless of whether those outputs are self-generated.** This supports the familiarity-as-mechanism hypothesis.
- The effect is robust across the tested settings — suggesting self-preference is best operationalised as "perplexity preference" rather than authorship recognition.

> [!warning] Limitations
> - Study focuses on GPT-4; generalisation to smaller judges or other model families is not fully characterised.
> - Primarily dialogue-system evaluation; transfer to domain-specific high-stakes judgment is untested.
> - "Perplexity under judge" requires access to the judge's logprobs, which may not be available in API-only deployments — limiting the metric's direct applicability as a live de-biasing tool.

> [!question] Open questions
> - Can a perplexity-based correction term de-bias judge scores post-hoc?
> - Does familiarity bias interact with [[position-bias-llm-judge]] — are both driven by a common "path of least resistance" computation?
> - For custom fine-tuned or RLHF'd judges, does familiarity bias shift toward RLHF-favoured patterns rather than low-perplexity ones?

> [!tip] My take
> The reframing from "self-preference" to "familiarity/perplexity preference" is the important move — it means a calibration baseline cannot be rescued by simply anonymising authorship. Any GE that shares a base model family with the Prompt Refiner's output model is structurally at risk of over-rewarding outputs that look like its own generations. For [[golden-evaluator-calibration-baseline]], this is an argument for using a GE from a **different model family** than the system under evaluation, or explicitly measuring perplexity distribution alignment.

## Related

- [[llm-as-judge]] (foundation — derived_from)
- [[self-preference-bias]]
- supports: [[llm-judges-over-reward-low-perplexity-outputs]]
- related: [[position-bias-llm-judge]]
