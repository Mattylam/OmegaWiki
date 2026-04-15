---
title: "Extending SMT compliance verification with a minimal-modification repair step converts binary verdicts into actionable compliance diagnostics that localise which specific facts drive non-compliance"
slug: minimal-modification-smt-repair-enables-actionable-compliance-diagnostics
status: weakly_supported
confidence: 0.65
tags: [neurosymbolic, llm-symbolic-solver, smt, compliance-verification, repair, minimal-modification]
domain: NLP
source_papers: [neuro-symbolic-compliance-integrating-llms-smt]
evidence:
  - source: neuro-symbolic-compliance-integrating-llms-smt
    type: supports
    strength: moderate
    detail: "Neuro-Symbolic Compliance framework evaluated on 87 Taiwan FSC enforcement cases. Minimal-modification formulation computes the smallest change to case facts required to restore legality; achieves 86.2% SMT generation correctness and 100×+ reasoning efficiency vs LLM-only. Consistently produces actionable repair suggestions."
conditions: "Demonstrated on structured financial regulation with 87-case corpus. The 'minimal' metric is a design choice over the fact schema, not ontologically given. Transfer to domains with more discretionary rule structures (healthcare malpractice, professional ethics) is unclear. 13.8% residual SMT-translation error means repair suggestions on those cases derive from wrong premises."
date_proposed: 2026-04-15
date_updated: 2026-04-15
---

> [!abstract] Statement
> A compliance-verification pipeline that couples LLM autoformalization with an SMT solver can be extended beyond binary compliance verdicts: by formulating the non-compliance case as an optimisation problem over the fact schema, the solver computes the smallest factual modification that would restore compliance. This converts "this is non-compliant" into "specifically these facts drive non-compliance; changing them would restore compliance" — an actionable diagnostic suitable for downstream repair, correction, or explanation.

## Evidence summary

- Neuro-Symbolic Compliance on 87 Taiwan FSC enforcement cases
- 86.2% SMT generation correctness, 100×+ efficiency vs LLM-only reasoning
- Minimal-modification outputs provide actionable repair localisations

> [!info] Conditions and scope
> - Structured financial regulation with a well-defined fact schema
> - "Minimal" is operationalised via a schema-level distance metric — a design choice with downstream consequences
> - Pipeline inherits the translation-layer error rate; minimal-modification from a wrong translation produces wrong repairs

> [!warning] Counter-evidence
> - Preliminary-foundation evaluation corpus (n=87); replication on other regulatory frameworks pending
> - Domains with discretionary rather than bright-line rules may not admit a meaningful "minimal modification" — the notion assumes a geometric structure over fact space

## Linked ideas

- [[decomposed-reference-based-golden-evaluator]] — motivates using minimal-modification repair as a per-claim diagnostic on the calibration card: when an agent bullet is SMT-non-compliant with the UW rule KB, report which specific sub-fact would need to change, rather than just a Boolean correctness verdict
- [[per-case-smt-routed-correctness-branch]] — adopts minimal-modification as Stage D's diagnostic output on `unsatisfiable` verdicts; localises failure to specific facts/claims

> [!question] Open questions
> - Does the approach transfer to domains with less rigid rule structures?
> - Can minimal-modification output be paired with natural-language rationale ("changing X would restore compliance because Y") for human-facing diagnostics?
