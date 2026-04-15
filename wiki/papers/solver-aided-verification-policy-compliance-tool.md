---
title: "Solver-Aided Verification of Policy Compliance in Tool-Augmented LLM Agents"
slug: solver-aided-verification-policy-compliance-tool
arxiv: "2603.20449"
venue: "arXiv"
year: 2026
tags: [neurosymbolic, llm-symbolic-solver, z3, smt-lib, policy-compliance, tool-augmented-agents]
importance: 3
date_added: 2026-04-15
source_type: pdf
s2_id: ""
keywords: [policy compliance, SMT-LIB, Z3, tool-augmented agents, runtime verification, TauBench]
domain: NLP
code_url: ""
cited_by: []
---

## Problem

Tool-augmented LLM agents (customer service bots, process-automation workflows) operate under domain policies that specify what actions they may and may not take. Prior approaches enforce policy via prompt-level instructions or post-hoc monitoring — both provide no formal guarantee that policy violations will be prevented. For sensitive applications (financial, legal, healthcare, underwriting), a stronger guarantee is needed: policies must be formalised and enforced at the tool-call boundary, not requested of the LLM.

## Key idea

**Formalise policies once, enforce at runtime**. Use an LLM-assisted, human-guided offline stage to translate natural-language policies into SMT-LIB 2.0 constraints over a schema of agent state and tool arguments. At runtime, intercept every planned tool invocation, check it against the SMT constraints with Z3, and block violations before they execute. The agent still reasons with the LLM; the policy layer is external and formally sound.

## Method

Two-stage architecture:

1. **Offline formalisation** (human + LLM):
   - LLM assists a domain expert in translating NL policy text into SMT-LIB 2.0 constraints
   - Constraints operate over a declared schema (agent state variables, tool argument types)
   - Human expert reviews and corrects — the offline nature makes thorough human review feasible
2. **Runtime enforcement** (Z3):
   - Every planned tool call is intercepted before execution
   - The tool call's arguments and current agent state are bound to the schema
   - Z3 checks whether the call satisfies all constraints; violations are blocked

The architecture separates the translation concern (offline, human-supervised, high-investment) from the verification concern (runtime, automatic, fast).

## Results

- Evaluated on **TauBench** (tool-augmented agent benchmark)
- Reduces policy violations while maintaining task accuracy — the compliance improvement does not come at the cost of task success
- Demonstrates that formal verification can be integrated at runtime without prohibitive latency

> [!warning] Limitations
> - The abstract does not specify which policy types are handled well vs poorly; policies that require probabilistic or quantitative reasoning (e.g. "loss ratio must be < 0.75 on average across the portfolio") may exceed standard SMT-LIB expressiveness.
> - Offline formalisation is a material upfront cost and requires domain expert time — not zero-setup.
> - TauBench is a general-purpose tool-agent benchmark; transfer to specialist compliance settings (healthcare HIPAA, financial regulatory, underwriting guidelines) is not directly demonstrated.
> - Intercepting tool calls assumes a controllable execution environment; agents that cannot be sandboxed cannot use this enforcement point.

> [!question] Open questions
> - What's the optimal granularity of the policy schema — too coarse misses violations, too fine blows up the constraint space?
> - Can policy formalisation be incrementally updated (e.g. when a guideline changes) without re-doing the whole KB?
> - How does runtime verification interact with long-horizon agent plans where a single tool call may be part of a multi-step trajectory — must the constraint check consider only the immediate call, or the full plan?

> [!tip] My take
> For the underwriting setting, this is the closest-fit architectural template. UW guidelines ("HS 21 38 is mandatory when operation = residential construction", "DNW if window/door installation is primary focus") are exactly the kind of policy this paper's approach formalises. The offline/runtime split is important: formalising UW guidelines is a material engineering investment, but it's a **static** investment against a relatively stable rule base. Once done, runtime verification of agent outputs is cheap per-call.
>
> For [[decomposed-reference-based-golden-evaluator]]'s correctness branch, this suggests a concrete implementation path:
> (1) Offline: human + LLM formalise the UW rule corpus into SMT-LIB (mandatory endorsements, DNW list, exposure classes).
> (2) Runtime: for each formalisable claim in $\hat{y}$ (detected by a classifier), bind to the schema and Z3-check against the formal rule base.
> (3) Non-formalisable claims stay with the three-way NLI branch (current Stage 2).
>
> The offline cost is non-trivial but is incurred once, not per-evaluation.

## Related

- [[llm-as-judge]] (foundation — derived_from)
- [[neurosymbolic-llm-verification]]
- [[autoformalization]]
- [[policy-compliance-smt-formalization]]
- supports: [[policy-rules-can-be-formalized-as-smt-constraints]]
