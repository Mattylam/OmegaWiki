---
title: "Bootstrap (DSPy)"
aliases: ["bootstrapping", "BootstrapFewShot", "bootstrapped demonstrations", "trace-based compilation", "self-bootstrapping", "automated demonstration generation"]
tags: [few-shot-learning, prompt-optimization, automated-example-selection, dspy]
maturity: emerging
key_papers: [dspy-compiling-declarative-language-model-calls]
first_introduced: "2023"
date_updated: 2026-04-13
related_concepts: []
---

## Definition

**Bootstrapping in DSPy** is the automated process of creating, collecting, and selecting high-quality demonstrations (example traces) to teach modules how to perform specific tasks within a pipeline. The teleprompter runs the program on a small set of training inputs, records the traces of every internal module call, filters by a validation metric, and keeps successful intermediate steps as few-shot demonstrations. It enables a program to "self-improve" without requiring humans to write manual examples for every intermediate step — missing labels for internal modules (e.g. rationales, search queries) are generated from successful executions.

> [!tip] Intuition
> Instead of a human cherry-picking "good examples" of chain-of-thought reasoning for a prompt, you let the model attempt the task on labelled data, filter for attempts that arrived at the correct answer, and use those attempts' intermediate reasoning as the few-shot demonstrations. The demonstrations are thus *grounded in real execution* — they are examples the model itself can reproduce, not idealised demonstrations a human wrote. This also solves the "missing intermediate labels" problem: you only need end-to-end task labels to bootstrap demonstrations for every intermediate step.

## Formal notation

Given a program $P$ with $K$ modules, a training set $D = \{(x_j, y_j)\}$, and a validation metric $m$:

$$\text{Training inputs} \xrightarrow{\text{simulate + trace}} \text{Candidate traces} \xrightarrow{\text{metric filter}} \text{Bootstrapped demonstrations}$$

For each run of $P$ on training input $x_j$ producing output $\hat{y}_j$ and trace $\tau_j = (t_j^1, \ldots, t_j^K)$ (one sub-trace per module), we keep the trace iff $m(\hat{y}_j, y_j) = 1$. Successful $\tau_j$ contribute their per-module sub-traces $t_j^k$ to the demonstration pool for module $k$. The teleprompter then selects (typically) the best $n$ demonstrations per module to inject into future prompts.

## Variants

- **`BootstrapFewShot`**: the canonical variant. Collects traces from $D_{\text{train}}$, filters by $m$, injects top traces as few-shot demonstrations. Parameter `max_bootstrapped_demos` caps traces per module.
- **`BootstrapFewShotWithRandomSearch`**: generates multiple configurations (different seeds, demo counts) and keeps the best. Costs more compile-time LLM calls but reliably squeezes additional accuracy.
- **`BootstrapFinetune`**: uses bootstrapped traces to fine-tune a smaller LM instead of just selecting demonstrations. The demonstrations become training examples for supervised fine-tuning.
- **Self-teaching / teacher-student**: a previously bootstrapped program acts as a "teacher" to train a "student" program on more complex behaviours.
- **Constrained bootstrapping**: demonstrations filtered not just by metric but by additional constraints (e.g. must match a format, must not use certain tools).

## Comparison

| Mechanism | Demonstration source | Labels needed | Reliance on human judgement |
|---|---|---|---|
| Hand-written few-shot | Developer writes examples | No | High — every example is hand-crafted |
| LabeledFewShot | Random sample from labelled set | Yes, per-example | Low |
| **Bootstrap (DSPy)** | Successful execution traces | Only end-to-end | Low — metric decides success |
| Fine-tuning | Labelled pairs drive gradient updates | Yes, per-example | Low, but updates model weights |

## When to use

- When you have at least a small labelled training set (20-50 examples for BootstrapFewShot to be effective)
- When the baseline program already has > 0% success rate (there need to be successful traces to harvest)
- When the pipeline has intermediate steps for which you don't have direct labels (bootstrap generates them from end-to-end labels)
- When the validation metric reliably identifies successful runs — a noisy metric produces noisy bootstrap output

> [!warning] Known limitations
> - Requires non-zero baseline success rate — if the program never succeeds, there are no traces to bootstrap from
> - Validation metric quality is the ceiling; a metric with false positives contaminates demonstrations
> - Compile-time LLM calls scale with training-set size; for expensive base models, cost can be significant
> - Bootstrapped demonstrations may overfit to quirks of the training distribution
> - No principled stopping criterion — when are "enough" demonstrations collected?

> [!question] Open problems
> - Sample-efficient bootstrapping for rare-success tasks (where baseline is near 0%)
> - Robustness to metric noise — can the filter itself be made adversarial to weed out false positives?
> - Combining bootstrapped demonstrations with hand-crafted seeds for low-data regimes
> - Theoretical analysis of why bootstrapped examples transfer better than hand-written ones (the empirical result is robust; the mechanism is not formally characterised)

## Key papers

- [[dspy-compiling-declarative-language-model-calls]] (2023) — introduces bootstrapping as the technical engine behind DSPy teleprompters; empirically validates the gains across GSM8K, HotPotQA

> [!tip] My understanding
> Bootstrap-in-DSPy is the concrete mechanism that operationalises the claim that "automated example selection beats hand-written examples" ([[bootstrapping-produces-better-demonstrations-than-hand-written]]). For the self-learning project's Phase 2, the V1 data collection pipeline is explicitly designed to produce the exact record type BootstrapFewShot consumes: `(feedback, task_output)` → `(is_addressed, classification, reason)` with the developer's approve/reject as the metric. Developer-override records are the most valuable bootstrap seeds because they are cases where the original LLM trace failed the metric and the developer provided the correction — these are exactly the "hard cases" DSPy's extended bootstrappers (e.g. with constrained sampling) target.
