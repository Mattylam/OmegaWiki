---
title: "Autoformalization"
aliases: ["NL to formal logic", "natural language formalization", "NL-to-SMT", "semantic parsing to logic", "autoformalize"]
tags: [neurosymbolic, llm-symbolic-solver, semantic-parsing, formal-verification]
maturity: active
key_papers: [logic-lm-empowering-llms-symbolic-solvers, proof-thought-neurosymbolic-program-synthesis]
first_introduced: "2023"
date_updated: 2026-04-15
related_concepts: [neurosymbolic-llm-verification]
---

## Definition

Autoformalization is the automatic translation of natural-language text into a formal logical representation (first-order logic, SMT-LIB, constraint systems, theorem-prover syntax) suitable for symbolic reasoning. In the LLM era, the LLM is the translator, replacing hand-crafted semantic parsers. The output is a machine-readable formalism a downstream solver can reason over.

> [!tip] Intuition
> Autoformalization is the LLM-era revival of a classical NLP problem — turn "every tall person is friendly" into `∀x. Tall(x) → Friendly(x)`. What changed is that LLMs can do this with few-shot examples across open domains, whereas earlier semantic parsers required per-domain training. What didn't change is that translation errors are hard to detect without ground truth — a syntactically-valid, semantically-wrong formalization looks identical to a correct one from the solver's perspective.
> *Source: LLM analysis*

## Formal notation

$$\text{autoform}: (x, \mathcal{L}) \mapsto \phi \in \mathcal{L}$$

where $x$ is NL text and $\mathcal{L}$ is a target formal language (FOL, SMT-LIB, Prolog, etc.). The quality of $\phi$ is measured by:

- **Parse rate**: fraction of $\phi$ accepted by the solver without syntactic error
- **Semantic faithfulness**: fraction of $\phi$ that accurately encodes $x$'s propositional content (the hard metric — requires ground-truth formalizations or human annotation)
- **Downstream accuracy**: fraction of solver outputs that match the NL-task's correct answer

## Key variants

- **Direct LLM autoformalization**: single-shot or few-shot prompt; LLM emits $\phi$ directly
- **DSL-mediated** (Proof of Thought): LLM emits an intermediate DSL (JSON-based); deterministic interpreter converts to $\phi$
- **Step-by-step** (NL2FOL): decompose NL into atomic propositions first, formalize each in isolation, compose into $\phi$
- **Human-guided**: domain expert supervises LLM output and curates a formal KB; used when the KB is static and the cost of one-time correctness exceeds the cost of re-formalization

## Comparison

| Style | Speed | Translation reliability | Requires domain expert |
|---|---|---|---|
| Direct LLM | Fast | Moderate; error-prone on nested/quantified structures | No |
| DSL-mediated | Medium | Higher; interpreter enforces type safety | No at runtime; yes at DSL design |
| Step-by-step | Slow | Higher on atomic claims; composition can fail | No |
| Human-guided | Slow | Highest | Yes |

## When to use

- As the first stage of any [[neurosymbolic-llm-verification]] pipeline
- For compliance-checking against a formalized rule base
- To construct an initial formal KB from a policy document (offline, with human review)

> [!warning] Known limitations
> - **Silent semantic errors** — a formalization can parse cleanly and solve but misrepresent the NL; no solver signal catches this
> - **Domain transfer gap** — LLM autoformalization accuracy drops on specialist text (legal, medical) where general-purpose training leaves domain ontology gaps
> - **Quantifier and scope ambiguity** — natural-language quantifiers map to multiple logical readings; LLMs often default to the most common one, missing domain-standard readings
> - **Open-vs-closed world** — many NL statements assume closed-world semantics (things not mentioned are false); formal logic defaults to open-world; the gap produces systematic translation errors
> *Source: LLM analysis*

> [!question] Open problems
> - How to calibrate the autoformalization step independently — an analogue of [[decomposition-quality-metrics]] for translation quality
> - Detecting silent semantic errors without ground-truth formalizations (via consistency checks across multiple LLM translations of the same text, perhaps)
> - Scaling to partially-formalizable documents with principled handling of non-formalizable residue
> *Source: LLM analysis*

## Key papers

- [[logic-lm-empowering-llms-symbolic-solvers]] — canonical LLM-era autoformalization framework; five solver backends; self-refinement via solver error feedback
- [[proof-thought-neurosymbolic-program-synthesis]] — DSL-mediated autoformalization with typed interpreter; JSON DSL + Z3
- [[neuro-symbolic-compliance-integrating-llms-smt]] — specialist-domain autoformalization; reports 86.2% SMT-generation correctness on Taiwan FSC enforcement corpus as a real-world translation-accuracy data point

> [!tip] My understanding
> For the SMT-routed correctness branch in [[decomposed-reference-based-golden-evaluator]], autoformalization is where most of the engineering effort lands. Two design choices matter: (a) DSL-mediated vs direct — DSL reduces translation surface but requires upfront DSL design; (b) online (autoformalize each agent claim at runtime) vs offline-KB (autoformalize the UW rule base once, match agent claims against formalized rules at runtime). For the UW setting, offline-KB is likely the right default — rules are static, claims are dynamic.
