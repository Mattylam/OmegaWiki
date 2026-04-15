---
title: "LLM-Judge Bias Taxonomy (CALM)"
aliases: ["CALM framework", "judge bias taxonomy", "llm judge bias categories", "CALM bias evaluation"]
tags: [llm-as-judge, bias, evaluation-framework, taxonomy]
maturity: active
key_papers: [justice-prejudice-quantifying-biases-llm-judge]
first_introduced: "2024"
date_updated: 2026-04-15
related_concepts: [position-bias-llm-judge, self-preference-bias]
---

## Definition

A structured taxonomy of 12 distinct biases affecting LLM-as-judge systems, paired with an automated probe-based evaluation framework (CALM) for quantifying each bias without human annotation. The taxonomy provides a shared vocabulary for bias diagnosis and a reproducible benchmark protocol.

> [!tip] Intuition
> Prior work on judge biases was a cottage industry of one-paper-per-bias. The taxonomy's value is not that each of its 12 categories is individually novel, but that having a *complete inventory* lets a calibration study say "we tested for all 12, here are the weak spots" rather than "we found some biases". It turns bias diagnosis into a checklist.
> *Source: LLM analysis*

## Formal notation

For each bias category $c$ and judge $J$, CALM constructs a probe set $P_c$ where the unbiased verdict is definitionally correct. The bias score is the fraction of probes on which $J$ deviates from the unbiased verdict:

$$\text{Bias}_c(J) = \frac{1}{|P_c|} \sum_{p \in P_c} \mathbb{1}[J(p) \neq \text{unbiased}(p)]$$

A robustness score $1 - \text{Bias}_c(J)$ near 1.0 indicates the judge is largely immune to that bias.

## Key variants

The 12 bias categories:

1. **Position** — preference based on order of candidates (see [[position-bias-llm-judge]])
2. **Verbosity** — preference for longer outputs regardless of content
3. **Compassion-Fade** — treating anonymised candidates differently than named ones
4. **Bandwagon** — following claimed-majority opinion
5. **Distraction** — being swayed by irrelevant inserted content
6. **Fallacy-Oversight** — accepting logically fallacious but coherent reasoning
7. **Authority** — over-weighting claimed expert credentials
8. **Sentiment** — preference for positive-toned outputs
9. **Chain-of-Thought** — rewarding visible reasoning regardless of correctness
10. **Self-Enhancement** — preference for own-generated text (overlaps with [[self-preference-bias]])
11. **Refinement-Aware** — bias when told an output is a refinement
12. **Diversity** — differential treatment across demographic attributes

## Comparison

| Diagnostic approach | Human-free | Reproducible | Coverage |
|---|---|---|---|
| Ad-hoc per-bias studies | ✗ (usually) | ✗ | Narrow |
| CALM probes | ✓ | ✓ | Broad (12 categories) |
| Full human-UW calibration | ✗ | ✗ | Real-world, but expensive |

## When to use

Use CALM as a **pre-flight check** on any LLM judge before deploying it as an oracle. CALM complements — does not replace — human calibration: a judge can pass CALM (low bias on synthetic probes) while still disagreeing with human experts on real judgment tasks.

> [!warning] Known limitations
> - Probes are synthetic; bias scores measure robustness on constructed probes, not real-world prevalence.
> - Interactions between biases are not directly measured.
> - Task-specific bias patterns (e.g. domain-specific verbosity preference) may not be captured by generic probes.
> *Source: LLM analysis*

> [!question] Open problems
> - Mapping CALM bias scores to real-world failure rates
> - Using CALM probes as an adversarial training signal for judge robustness
> - Identifying latent factor structure among the 12 categories (are there really 12, or 3-4 underlying dimensions?)
> *Source: LLM analysis*

## Key papers

- [[justice-prejudice-quantifying-biases-llm-judge]] — introduces the 12-category taxonomy and CALM framework, evaluates 6 LLM judges

> [!tip] My understanding
> For [[golden-evaluator-calibration-baseline]] Step 4 (diagnostic failure-mode classification), the current three-category sketch (sycophancy, adversarial response, stylistic bias) should be widened to the CALM 12. In particular, Fallacy-Oversight and Verbosity are under-recognised failure modes for high-stakes judgment: a UW output that is logically fallacious but verbose-and-structured will likely be rewarded by a naive GE.
