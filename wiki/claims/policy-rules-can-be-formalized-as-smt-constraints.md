---
title: "Natural-language policy and compliance rules can be formalised into SMT-LIB constraints with LLM + human assistance, enabling runtime enforcement that reduces violations without sacrificing task accuracy"
slug: policy-rules-can-be-formalized-as-smt-constraints
status: supported
confidence: 0.75
tags: [neurosymbolic, llm-symbolic-solver, smt-lib, policy-compliance, runtime-verification]
domain: NLP
source_papers: [solver-aided-verification-policy-compliance-tool]
evidence:
  - source: solver-aided-verification-policy-compliance-tool
    type: supports
    strength: moderate
    detail: "LLM-assisted human-supervised translation of NL policies into SMT-LIB 2.0 constraints, evaluated on TauBench tool-augmented agent benchmark. Runtime Z3-based interception of tool calls reduces policy violations while maintaining task accuracy."
conditions: "Demonstrated for general-purpose tool-augmented agent policies on TauBench. Expressiveness is bounded by standard SMT-LIB theories (predicate logic + arithmetic, arrays, strings); probabilistic, temporal, or intent-based rules require other formalisms. Offline formalisation is a material upfront cost. Requires a controllable execution environment for runtime interception."
date_proposed: 2026-04-15
date_updated: 2026-04-15
---

> [!abstract] Statement
> For domains with relatively stable rule bases, natural-language policies can be translated offline into SMT-LIB constraints through LLM-assisted human-supervised authoring. At runtime, agent actions are checked against the resulting formal KB via an SMT solver (Z3) before execution. This pattern reduces policy violations while preserving task accuracy, and amortises the offline formalisation cost across every runtime check.

## Evidence summary

- Solver-Aided Policy Compliance demonstrates the pattern on TauBench
- Runtime violation rate decreases; task accuracy preserved
- Architecture separates offline translation (expensive, high-review) from runtime enforcement (cheap, automatic)

> [!info] Conditions and scope
> - SMT-LIB expressiveness bounded — probabilistic, temporal, intent-based rules require other formalisms
> - Requires controllable execution environment (ability to intercept tool calls)
> - Offline formalisation is not zero-cost — requires human domain expert time
> - Transfer to specialist compliance domains (healthcare, finance, underwriting) is plausible but not yet empirically demonstrated at the reported violation-reduction levels

> [!warning] Counter-evidence
> - Not yet catalogued. Possible counter-evidence: a domain where policy rules are predominantly intent-based or probabilistic, where SMT formalisation fails to capture meaningful rule content.

## Linked ideas

- [[decomposed-reference-based-golden-evaluator]] — motivates offline UW-rule-base formalisation + runtime SMT check as the correctness branch for formalisable claims
- [[per-case-smt-routed-correctness-branch]] — per-case lazy formalisation variant; KB seeded by $y^*$'s cited rules + agent-citation expansion, sidestepping full-corpus offline formalisation cost

> [!question] Open questions
> - Does the pattern maintain its violation-reduction performance on specialist domain policies (e.g. UW guidelines) vs general agent policies?
> - How does incremental policy update (guideline revision) interact with the formalised KB?
