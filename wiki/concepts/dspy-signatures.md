---
title: "DSPy Signatures"
aliases: ["signatures", "natural language typed signatures", "declarative signatures", "task signatures", "DSPy signature"]
tags: [declarative-programming, llm-programming, prompt-engineering, dspy]
maturity: emerging
key_papers: [dspy-compiling-declarative-language-model-calls]
first_introduced: "2023"
date_updated: 2026-04-13
related_concepts: []
---

## Definition

A **DSPy signature** is a natural-language typed declaration of a function — a short declarative spec that tells DSPy *what* a text transformation needs to do, without prescribing *how* an LM should be prompted to implement that behaviour. Formally, a signature is a tuple of input fields and output fields, with an optional task instruction and optional per-field descriptions. Field names carry semantic role information that the compiler exploits. A signature can be expressed as shorthand (`question -> answer`) or as a Python class with typed `dspy.InputField` / `dspy.OutputField` declarations.

> [!tip] Intuition
> A signature is to a DSPy program what a type signature is to a programming language — it says "this function takes a `question` and returns an `answer`" without committing to an implementation. Multiple DSPy modules (`Predict`, `ChainOfThought`, `ReAct`, `ProgramOfThought`) can all be instantiated from the *same* signature and used interchangeably, because the signature describes the interface, not the behaviour. This separation is what enables the compiler to optimise the implementation without the developer touching prompt strings.

## Formal notation

A signature $\sigma$ is a tuple $(\text{inputs}, \text{outputs}, \text{instruction?})$ where each of `inputs` and `outputs` is a list of fields $\{f_1, \ldots, f_n\}$. A module $M$ implementing $\sigma$ is a callable $M_\sigma: \prod_i \text{type}(\text{inputs}_i) \to \prod_j \text{type}(\text{outputs}_j)$. The compiler generates a prompt $p_\sigma$ at compile time; at runtime the prompt is filled with actual input values and parsed back into typed outputs.

## Variants

- **Shorthand signatures**: string notation like `"question -> answer"` or `"english_document -> french_translation"`. Field names carry semantic meaning the compiler uses to construct instructions.
- **Class-based signatures**: Python class subclassing `dspy.Signature`, with docstring as task instruction and `dspy.InputField` / `dspy.OutputField` declarations that can include `desc` metadata and types
- **Auto-expanded signatures** (by modules): `ChainOfThought` expands `question -> answer` into `question -> rationale, answer` internally, prepending a scratchpad instruction; the developer doesn't see this expansion

## Comparison

| Approach | What's specified | Who writes the prompt |
|---|---|---|
| Hand-crafted prompt templates | The full prompt string | Developer |
| DSPy signature | Input/output interface + task intent | Compiler |
| Function signature in programming | Input/output types | Language runtime |
| Neural network layer signature | Tensor shapes | Framework |

## When to use

- When you want the same pipeline to be portable across LMs (GPT-4 → Claude → local llama) without rewriting prompts
- When intermediate pipeline stages need structured typed outputs (not free-form strings)
- When you expect the prompt to be automatically optimised by a teleprompter later
- When multiple implementations (Predict vs ChainOfThought vs ReAct) should be interchangeable at the same pipeline position

> [!warning] Known limitations
> - Field names must carry clear semantic meaning — a signature `x -> y` is under-specified and the compiler may produce poor prompts
> - Optional `desc` metadata and task instructions matter more than the paper implies; omitting them shifts more work onto the compiler's inference
> - Not all LM behaviours are easily signature-describable — free-form creative generation or highly-structured multi-turn dialogue may be awkward to fit
> - Signature-to-prompt expansion is a black box; debugging a failing signature often requires inspecting the generated prompt

> [!question] Open problems
> - Optimal granularity of signatures — coarse (one big signature) vs fine (many small composable ones)
> - Typed signatures with rich type constraints (sum types, refinement types) — the DSPy paper defers this
> - Signature composition: when two signatures produce the same output type, can the compiler infer substitutability?

## Key papers

- [[dspy-compiling-declarative-language-model-calls]] (2023) — introduces the signature abstraction as the primary DSPy contribution alongside modules and teleprompters

> [!tip] My understanding
> Signatures are the DSPy equivalent of interfaces in OOP — they are what makes the whole programming model cohere. For the self-learning project, each pipeline step (Classifier step 1/2/3, Prompt Refiner, Output Evaluator, Regression Check) is a natural signature: `classifier_step_1: (feedback, task_output) -> (is_addressed: bool, reason: str)`. Writing these as DSPy signatures is the concrete first step toward enabling Phase 2 compilation. The typed output (bool, str) is also a gate against LLM output format drift — a significant failure mode in V1 today that signatures solve almost for free.
