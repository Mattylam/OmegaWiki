---
title: "Neuro-Symbolic Compliance: Integrating LLMs and SMT Solvers for Automated Financial Legal Analysis"
slug: neuro-symbolic-compliance-integrating-llms-smt
arxiv: "2601.06181"
venue: "arXiv"
year: 2026
tags: [neurosymbolic, llm-symbolic-solver, smt, financial-compliance, legal-analysis, minimal-modification]
importance: 3
date_added: 2026-04-15
source_type: pdf
s2_id: ""
keywords: [neuro-symbolic compliance, SMT, financial regulation, legal analysis, minimal modification, Taiwan FSC]
domain: NLP
code_url: ""
cited_by: []
---

## Problem

Financial regulatory compliance requires reasoning about statutes, enforcement cases, and facts to determine whether a given situation is legal, and — when it is not — what the least disruptive remediation would be. Prior LLM-based approaches lack formal verifiability: they produce explanations rather than guarantees, and they cannot compute *minimal* remediation. Conversely, pure symbolic approaches require hand-authored rule bases that do not scale to the size and churn of real financial law.

## Key idea

Couple an LLM-based interpretation layer with an **SMT solver that does two jobs**: (1) verify compliance given the facts and the formalised legal constraints, and (2) when non-compliance is detected, compute the **minimal factual modification** required to restore legality. The LLM is used once per legal artifact (statute or enforcement case) to produce SMT constraints; the solver then reasons over those constraints plus the instance facts.

## Method

Three-stage pipeline:

1. **Interpretation (LLM)**: the LLM reads statutes and enforcement cases, identifying the normative content (obligations, prohibitions, conditions, exceptions).
2. **Translation (LLM → SMT)**: the normative content is emitted as SMT constraints over a schema of legal facts.
3. **Verification & correction (solver)**: Z3 checks compliance of a specific instance; if non-compliant, solves an optimisation problem to find the smallest change to the instance's facts that restores compliance. "Smallest" is operationalised via a distance metric over the fact schema.

The minimal-modification formulation is the distinctive architectural commitment: it converts "is this legal?" from a binary verdict into an actionable repair suggestion.

## Results

- **Dataset**: 87 enforcement cases from Taiwan's Financial Supervisory Commission
- **SMT code generation correctness: 86.2%** — measures whether the LLM-to-SMT translation is itself correct
- **100×+ reasoning efficiency** vs LLM-only reasoning (the solver's structured search outperforms LLM free-form reasoning on these cases)
- Consistently corrects violations — when a case is non-compliant, the minimal-modification output identifies the specific facts whose change would restore compliance

> [!warning] Limitations
> - Self-described as a "preliminary foundation" — 87 cases is a modest evaluation corpus
> - 86.2% translation correctness means 13.8% of cases have silently-wrong SMT; the pipeline's outputs on those cases are sound-derivations-of-wrong-premises
> - Financial regulation has the advantage of relatively structured normative content; transfer to domains where regulations are more discretionary (healthcare malpractice standards, professional ethics) is unclear
> - Minimal-modification presupposes a metric over the fact schema — the metric is a design choice with downstream consequences (which modifications count as "minimal" is not ontologically given)

> [!question] Open questions
> - How robust is the 86.2% translation figure across regulatory frameworks with different drafting conventions?
> - Can the minimal-modification formulation be extended to **explain** why each modified fact is load-bearing, for human-facing applications?
> - Does the 100× efficiency gain hold on domains where the constraint space is larger (financial reg + tax + employment law simultaneously, e.g.)?

> [!tip] My take
> The minimal-modification primitive is more interesting for [[decomposed-reference-based-golden-evaluator]] than it might first appear. Naive compliance checking gives "agent output is non-compliant" as a Boolean; minimal-modification tells you **which specific claim in $\hat{y}$** would need to change to bring it into compliance. That's a much richer diagnostic for the GE calibration card — it converts correctness failure from "something is wrong" to "this specific proposition is wrong, and here's the minimal correction".
>
> The 86.2% SMT-translation correctness is also a useful data point: it quantifies the silent-translation-error rate for a real specialist domain, and suggests that a [[decomposition-quality-metrics]]-style calibration of the translation layer is warranted before deploying this in UW.

## Related

- [[llm-as-judge]] (foundation — derived_from)
- [[neurosymbolic-llm-verification]]
- [[autoformalization]]
- [[policy-compliance-smt-formalization]]
- extends: [[solver-aided-verification-policy-compliance-tool]]
- supports: [[minimal-modification-smt-repair-enables-actionable-compliance-diagnostics]]
