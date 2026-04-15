---
title: "Policy Compliance via SMT Formalization"
aliases: ["policy-as-SMT", "SMT-LIB policy constraints", "compliance verification with SMT", "rule-base SMT formalization", "formalised policy enforcement"]
tags: [neurosymbolic, llm-symbolic-solver, smt-lib, z3, policy-compliance, compliance-verification]
maturity: emerging
key_papers: [solver-aided-verification-policy-compliance-tool, neuro-symbolic-compliance-integrating-llms-smt]
first_introduced: "2026"
date_updated: 2026-04-15
related_concepts: [neurosymbolic-llm-verification, autoformalization]
---

## Definition

An architectural pattern in which a domain's natural-language policies (rules, regulations, guidelines) are formalised once into SMT-LIB constraints and subsequently enforced by a solver at runtime. The pattern separates policy-translation (offline, human-supervised, high-investment) from policy-enforcement (runtime, automatic, fast). It is distinguished from general [[neurosymbolic-llm-verification]] by its focus on **rule bases as static artifacts** rather than per-query formalisation.

> [!tip] Intuition
> If you formalise the rule base once, every compliance check is cheap. If you formalise per-query, every check is expensive and the LLM re-creates the same rules repeatedly with drift. For domains with stable rule bases — regulatory compliance, insurance underwriting, tax law, healthcare protocols — offline formalisation amortises the cost over every runtime check.
> *Source: LLM analysis*

## Formal notation

Offline stage: $\text{NL policies} \mapsto \Phi_{\text{KB}} \subset \text{SMT-LIB}$

Runtime stage (per check): given agent state $\sigma$ and action $a$, check $\Phi_{\text{KB}} \models \text{valid}(\sigma, a)$ via a solver.

Compliance: action $a$ is allowed iff the solver returns UNSAT on $\Phi_{\text{KB}} \cup \{\neg\text{valid}(\sigma, a)\}$.

## Key variants

- **Offline human-supervised formalisation** (Solver-Aided Policy Compliance): expert curates the KB with LLM assistance; runtime purely automatic
- **LLM-only online formalisation** (less robust): LLM formalises per-query without offline KB curation — fast but error-prone and redundant
- **Minimal-modification repair** (Neuro-Symbolic Compliance for Financial Legal): solver not only blocks violations but computes the smallest change to restore compliance
- **Schema-bound vs free-form**: constraints operate over a declared schema of state/action types (tight) vs free-form predicates (flexible but brittle)

## Comparison

| Strategy | Offline cost | Runtime cost | Robustness | Updateability |
|---|---|---|---|---|
| No formalisation (prompt-only) | Low | Low | Low | High |
| Offline formalisation (this pattern) | High | Low | High | Medium (requires re-review on rule change) |
| Per-query LLM formalisation | Low | High | Medium | High |

## When to use

- Domains with **stable** rule bases and high compliance cost (regulatory, legal, insurance, healthcare)
- Settings where violations must be blocked **before** execution, not detected after
- Applications where human experts are available for offline KB review

> [!warning] Known limitations
> - **Policy expressiveness**: standard SMT-LIB handles predicate logic + theories (arithmetic, arrays, strings) well but struggles with probabilistic, temporal, or narrative-intent rules
> - **Schema design is a one-time cost**: poorly-chosen schema constrains what policies can be expressed; re-design is expensive
> - **Rule updates require re-review**: a changed policy means re-translating affected constraints and re-validating
> - **Schema-to-runtime-state binding**: the mapping between agent state and the SMT schema must be maintained as the agent's internal representation evolves
> *Source: LLM analysis*

> [!question] Open problems
> - Incremental policy updates without full KB re-verification
> - Handling policies that mix deterministic rules (formalise cleanly) with discretionary/intent-based rules (don't)
> - Optimal division of labour between offline formalisation and runtime NLI fallback for hybrid policies
> *Source: LLM analysis*

## Key papers

- [[solver-aided-verification-policy-compliance-tool]] — offline NL → SMT-LIB formalisation + Z3 runtime enforcement; TauBench evaluation
- [[neuro-symbolic-compliance-integrating-llms-smt]] — financial legal compliance with minimal-modification repair; extends the pattern to produce actionable diagnostics, not just Boolean verdicts

> [!tip] My understanding
> For [[decomposed-reference-based-golden-evaluator]]'s SMT-routed correctness branch applied to UW evaluation, this pattern gives the most cost-effective design. UW guidelines are stable-enough that offline formalisation pays off, and compliance checks on agent output are structurally compatible with the runtime-enforcement pattern. The main engineering work is schema design (operation types, endorsement codes, exposure classes, jurisdiction rules) and initial KB translation; after that, per-evaluation compliance is cheap.
