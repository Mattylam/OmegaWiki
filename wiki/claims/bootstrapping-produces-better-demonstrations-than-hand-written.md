---
title: "Bootstrapping produces better few-shot demonstrations than hand-written examples"
slug: bootstrapping-produces-better-demonstrations-than-hand-written
status: weakly_supported
confidence: 0.65
tags: [bootstrapping, few-shot-learning, prompt-optimization, automated-example-selection]
domain: NLP
source_papers: [dspy-compiling-declarative-language-model-calls]
evidence:
  - source: dspy-compiling-declarative-language-model-calls
    type: supports
    strength: moderate
    detail: "DSPy's BootstrapFewShot selects demonstrations from successful execution traces rather than hand-written examples; ablations in Sec 6-7 show bootstrapped demonstrations drive the majority of quality gains (GPT-3.5: +49 pp on GSM8K) and outperform hand-crafted expert demonstrations by 5-46% on GPT-3.5. Can also generate missing labels for intermediate pipeline steps (rationales, search queries)."
conditions: "The bootstrapper needs (a) a training set where at least some runs succeed under the validation metric (so there are successful traces to harvest), (b) a metric that correctly identifies successful runs — a noisy metric leads to noisy demonstrations. Holds for tasks where execution traces are meaningful (i.e. step-by-step problem solving, multi-hop retrieval); unclear whether it helps when each task is essentially one call."
date_proposed: 2026-04-13
date_updated: 2026-04-13
---

> [!abstract] Statement
> Few-shot demonstrations selected automatically from successful execution traces — the bootstrapping approach used by DSPy teleprompters — produce higher-quality prompts than demonstrations that are manually hand-crafted by human experts, for pipelines where the validation metric can reliably score end-to-end outputs.

## Evidence summary

**Supporting evidence:**
- **[[dspy-compiling-declarative-language-model-calls]]** (2023): the paper explicitly frames bootstrapping as the mechanism through which DSPy achieves its gains over expert-crafted prompts. Ablations across multiple pipelines (ChainOfThought, multi-hop retrieval, agent loops) consistently show bootstrapped demonstrations are the decisive factor. Key qualitative insight: hand-written examples tend to be *idealised* (cherry-picked to look clean), while bootstrapped demonstrations are grounded in real execution traces that actually caused the model to produce correct outputs — so they transfer better to the deployment distribution.

> [!info] Conditions and scope
> Requires:
> - Successful traces exist in the training set (i.e. baseline performance > 0)
> - The validation metric correctly identifies success (noisy metric → noisy bootstrap)
> - The pipeline has meaningful intermediate steps from which to harvest traces
>
> The claim is specifically about few-shot example *selection*. It does not assert:
> - That bootstrapping is always superior to fine-tuning
> - That bootstrapping works without any labelled data (you still need a metric)
> - That bootstrapped demonstrations transfer across different tasks

> [!warning] Counter-evidence
> Hand-crafted examples may still win when:
> - The training-set success rate is near zero (no traces to bootstrap from)
> - The task is narrow enough that a domain expert's intuition about canonical examples is strong
> - The deployment distribution differs substantially from the bootstrap distribution
>
> No direct counter-evidence in ingested literature yet.

## Linked ideas

- [[golden-evaluator-calibration-baseline]] — prerequisite: establish GE reliability before using it as a bootstrap validation metric
- [[taxonomic-feedback-prompt-refiner]] — closed-vocabulary feedback schema that makes GE output directly BootstrapFewShot-compilable
- [[pairwise-preference-feedback-prompt-refiner]] — preference-based alternative when any y*-leakage is unacceptable
- [[distilled-rationale-feedback-prompt-refiner]] — two-stage evaluator with audited distillation

Directly relevant to Phase 2 of the self-learning project: developer-override records (where the LLM was wrong and the developer corrected it) are by definition *failure traces* — but the developer's corrected output is a labeled successful example. Bootstrapping in this setting should use developer-corrected final prompts, not raw LLM outputs, as the demonstrations.

> [!question] Open questions
> - Optimal blend of bootstrapped vs. hand-written demonstrations (the paper argues all-bootstrapped; mixed strategies may work better in low-data regimes)
> - Sensitivity to validation metric noise — how robust is bootstrapping when the metric has false positives?
> - Can bootstrapping discover *better* reasoning patterns than human experts produce, or does it just find valid ones faster?
