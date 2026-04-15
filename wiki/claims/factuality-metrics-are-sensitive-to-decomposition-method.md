---
title: "Atomic-fact-based factuality metrics (FActScore and kin) are materially sensitive to the decomposition method used; decomposer is a first-class pipeline component that must be independently calibrated"
slug: factuality-metrics-are-sensitive-to-decomposition-method
status: supported
confidence: 0.8
tags: [factuality, atomic-facts, decomposition, reliability, pipeline-calibration]
domain: NLP
source_papers: [closer-look-claim-decomposition]
evidence:
  - source: closer-look-claim-decomposition
    type: supports
    strength: strong
    detail: "Empirically demonstrates that FActScore values shift across decomposition methods on the same generations. Attribution of overall text support to the generation model conflates generator error and decomposer error. Proposes DecompScore to make decomposer quality auditable."
conditions: "Demonstrated on long-form generation factuality with single-shot LLM decomposers vs a logical-atomism / neo-Davidsonian decomposer. The specific magnitude of the shift is setup-dependent; the existence of the shift is a general finding that carries to any atomic-fact pipeline."
date_proposed: 2026-04-15
date_updated: 2026-04-15
---

> [!abstract] Statement
> When a factuality or completeness metric operates by decomposing a text into atomic facts and verifying each, the choice of decomposer materially affects the metric's output. Two different decomposers applied to the same text can produce different fact sets, and consequently different factuality scores, even though the generated text being evaluated is identical. The decomposer is therefore a first-class pipeline component: it contributes its own bias and variance to downstream metrics, and must be calibrated and reported separately rather than treated as a fixed preprocessing step.

## Evidence summary

- Empirical demonstration across multiple decomposition methods on the same generations
- Attribution ambiguity: score changes may reflect generator behaviour or decomposer behaviour
- DecompScore proposed as an explicit metric of decomposition quality, making the decomposer's contribution visible

> [!info] Conditions and scope
> - Applies to any pipeline where atomic fact / atomic claim decomposition precedes per-fact validation
> - Includes FActScore, VeriScore, and the completeness / correctness components of [[decomposed-reference-based-golden-evaluator]]
> - Magnitude of the sensitivity is setup-dependent; the structural claim (decomposer = confound) is general

> [!warning] Counter-evidence
> - Not yet catalogued. A counter-example would be a decomposer–generator pair where FActScore is provably decomposer-invariant; no such pair is documented in the current literature.

## Linked ideas

- [[decomposed-reference-based-golden-evaluator]] — this claim upgrades the "extractor reliability" risk from a soft caveat to a named calibration prerequisite. Before running GE calibration against UWs, the decomposer must be benchmarked for recall / precision against UW-annotated gold decompositions and for run-to-run consistency.
- [[golden-evaluator-calibration-baseline]] — per-component calibration card should include decomposer metrics, not only judge-vs-UW agreement

> [!question] Open questions
> - What is a transferable decomposer-calibration protocol for specialist domains (underwriting, legal, medical)?
> - How do we disentangle decomposer bias from generator behaviour post-hoc when a score appears to degrade?
