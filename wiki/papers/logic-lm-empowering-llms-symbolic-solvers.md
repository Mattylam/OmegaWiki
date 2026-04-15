---
title: "Logic-LM: Empowering Large Language Models with Symbolic Solvers for Faithful Logical Reasoning"
slug: logic-lm-empowering-llms-symbolic-solvers
arxiv: "2305.12295"
venue: "EMNLP Findings"
year: 2023
tags: [neurosymbolic, llm-symbolic-solver, autoformalization, faithful-reasoning, logical-reasoning]
importance: 5
date_added: 2026-04-15
source_type: pdf
s2_id: ""
keywords: [Logic-LM, symbolic solver, LLM reasoning, autoformalization, self-refinement, faithful reasoning]
domain: NLP
code_url: "https://github.com/teacherpeterpan/Logic-LLM"
cited_by: []
---

## Problem

LLMs display human-like reasoning on surface tasks but make unfaithful derivations — their stated conclusion does not always follow from their reasoning chain. They are black-box probabilistic models with no structural mechanism to enforce validity. Chain-of-thought prompting narrows the gap but does not close it; the LLM is still free to derive conclusions that its own premises do not support.

## Key idea

Delegate the inference step to a deterministic **symbolic solver**. The LLM is used only where it is strong — translating natural-language problems into a formal symbolic representation. A solver (Z3, Prover9, Pyke, depending on the fragment) then performs the actual logical inference, guaranteeing faithfulness by construction. If the LLM's translation is malformed, the solver returns a parse/type error that the LLM uses to self-refine the formalization.

## Method

Three-stage pipeline:

1. **Autoformalization** (LLM): read the NL problem, emit a symbolic representation in a form appropriate for the problem type — first-order logic (FOL), constraint satisfaction (CSP), SAT, or logic programming.
2. **Symbolic inference** (solver): deterministic solver produces the answer.
3. **Self-refinement**: if the solver errors out (syntactic malformation, type mismatch, unsatisfiable constraint system), the LLM receives the error as feedback and revises the formalization.

Five solver backends are integrated, each targeting a different logical fragment; the prompt includes solver-specific grammar examples.

## Results

Evaluated on five logical-reasoning benchmarks (ProofWriter, PrOntoQA, FOLIO, LogicalDeduction, AR-LSAT):

- **+39.2% average accuracy** over direct LLM prompting
- **+18.4% average accuracy** over chain-of-thought prompting
- Self-refinement module improves both correctness and parse success rate — error-feedback loops recover many LLM translation mistakes without human intervention.

> [!warning] Limitations
> - Effectiveness depends on the LLM's ability to autoformalize correctly; specialist domain text (legal, medical, underwriting) was not evaluated and may stress the translation step.
> - Requires the problem to be fully formalizable in one of the supported logical fragments; partial-logic text (mostly-NL with embedded formal rules) is out of scope (see [[neurosymbolic-nl-formalization-verification]] for a later treatment).
> - Solver-backend selection is per-task — no unified mechanism to handle mixed-fragment problems.
> - Self-refinement relies on solver errors being informative; semantically-correct-but-wrong formalizations (no error raised, wrong answer) are not caught by the refinement loop.

> [!question] Open questions
> - How does the approach transfer to domain-specific rule-heavy text (e.g. regulatory compliance, underwriting guidelines)?
> - Can the formalization step be calibrated independently, the way [[decmetrics-structured-claim-decomposition-scoring-factually]] calibrates claim decomposers?
> - What is the best integration strategy when only a subset of claims in a text are formalizable — route formalizable claims to the solver and non-formalizable claims to NLI?

> [!tip] My take
> This is the seminal template for LLM-solver integration and the paper everyone else in this cluster cites. For [[decomposed-reference-based-golden-evaluator]]'s correctness stage, Logic-LM establishes the pipeline shape: autoformalize the agent claim, query a solver against a formalized rule base, treat solver output as the correctness verdict. Two caveats apply to our setting:
> (1) Logic-LM assumes the full problem is formalizable; underwriting text is only partially formalizable, so the pipeline needs a **routing layer** (formalizable claims → SMT, non-formalizable → NLI).
> (2) The 39% gain is measured against pure logical benchmarks; domain transfer to semi-formal UW text is not demonstrated and is an open empirical question.

## Related

- [[llm-as-judge]] (foundation — derived_from)
- [[neurosymbolic-llm-verification]]
- [[autoformalization]]
- supports: [[llm-solver-integration-improves-faithful-reasoning]]
