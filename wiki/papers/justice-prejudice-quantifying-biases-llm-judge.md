---
title: "Justice or Prejudice? Quantifying Biases in LLM-as-a-Judge"
slug: justice-prejudice-quantifying-biases-llm-judge
arxiv: "2410.02736"
venue: "arXiv"
year: 2024
tags: [llm-as-judge, bias, taxonomy, evaluation-framework, CALM]
importance: 4
date_added: 2026-04-15
source_type: pdf
s2_id: ""
keywords: [LLM-as-judge, bias taxonomy, CALM framework, automated evaluation, fairness]
domain: NLP
code_url: ""
cited_by: []
---

## Problem

Prior work on LLM-judge biases examines each failure mode (position, self-preference, verbosity, …) in isolation with bespoke experimental protocols. There is no unified taxonomy of judge biases and no scalable, human-free protocol to measure them across models.

## Key idea

Propose a systematic, 12-category taxonomy of LLM-judge biases and introduce **CALM** — an automated framework that synthesises targeted probe prompts for each bias category and scores a judge's robustness without human annotation. This converts bias diagnosis from an open-ended research question into a reproducible benchmark.

## Method

- **Bias taxonomy (12 types)**: Position, Verbosity, Compassion-Fade, Bandwagon, Distraction, Fallacy-Oversight, Authority, Sentiment, Chain-of-Thought, Self-Enhancement, Refinement-Aware, Diversity.
- **CALM framework**: for each bias category, synthesise controlled probes where the "correct" verdict is unambiguous if the judge is unbiased; measure the rate at which the judge deviates.
- Evaluate 6 popular LLM judges on alignment and fact datasets under the framework.

## Results

- Judge performance varies substantially by bias category and by model.
- Claude-3.5 achieves ~0.985 robustness on alignment; GPT-4o leads on fact-related probes (~0.977).
- **Fallacy-Oversight** is consistently weak across all tested judges — judges accept superficially coherent but logically-fallacious reasoning as correct.

> [!warning] Limitations
> - CALM probes are synthetic and may not reproduce the distribution of bias triggers encountered in real-world evaluation tasks.
> - Probes isolate each bias; interactions (e.g. position × verbosity) are not systematically measured.
> - Domain coverage centred on general alignment and fact benchmarks; not tailored to high-stakes specialist evaluation.

> [!question] Open questions
> - Which of the 12 biases most predict real-world judge failures vs. which are artefacts of the probe construction?
> - Can CALM-style probes be used as a training signal (adversarial fine-tuning for judge robustness) rather than only a diagnostic?
> - How do the 12 bias types interact — is there a low-dimensional latent structure?

> [!tip] My take
> This is the most useful taxonomy I've seen for diagnosing an LLM judge. For [[golden-evaluator-calibration-baseline]] Step 4 (failure-mode classification), the current sketch lists three diagnostic buckets (sycophancy, adversarial-response, stylistic bias) — replace that with the CALM 12-category schema to get a wider net. The Fallacy-Oversight finding is especially concerning for underwriter evaluation: UW reasoning must be logically sound, and a judge that rewards superficial coherence will reward a Prompt Refiner that optimises for plausible-sounding but wrong outputs.

## Related

- [[llm-as-judge]] (foundation — derived_from)
- [[llm-judge-bias-taxonomy]]
- [[position-bias-llm-judge]]
- [[self-preference-bias]]
- supports: [[llm-judges-exhibit-systematic-multi-category-biases]]
