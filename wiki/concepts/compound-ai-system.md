---
title: "Compound AI System"
aliases: ["compound AI", "compound system", "LLM pipeline", "multi-LLM system", "modular LLM system", "LLM program", "agentic compound system"]
tags: [llm-programming, llm-pipelines, agentic-systems, system-design, compound-ai-system]
maturity: active
key_papers: [dspy-compiling-declarative-language-model-calls, gepa-reflective-prompt-evolution-outperform-reinforcement]
first_introduced: "2024"
date_updated: 2026-04-17
related_concepts: [teleprompters, reflective-prompt-evolution, dspy-signatures]
---

## Definition

A **compound AI system** is a control-flow program whose primary computational units are one or more **prompted LLM modules** plus auxiliary tools (retrievers, code interpreters, calculators, search APIs). The system is defined by (a) the set of LLM modules and their prompts/parameters, (b) the tools, and (c) the deterministic control flow that routes data between them. The term contrasts with monolithic single-call LLM use ("ask the model once") and with end-to-end-trained neural networks ("one big model"); compound systems sit in between, exploiting modularity for both engineering tractability and quality.

> [!tip] Intuition
> A compound AI system is to an LLM what a software pipeline is to a single function. Just as you would not stuff a complex application into a single Python function, you should not stuff a complex task into a single LLM prompt. Decompose into modules — a classifier, a retriever, a reasoner, an evaluator — each with its own prompt, glued together with deterministic Python. Each module is independently testable, independently optimizable, and independently swappable. The compound is more robust than the sum of its parts because errors are caught at module boundaries, not at the end of a giant chain-of-thought.
>
> *Source: LLM analysis (term popularized by the BAIR blog post "The Shift from Models to Compound AI Systems", Zaharia/Khattab/Stoica et al. 2024)*

## Formal notation

A compound AI system is a tuple $S = (V, E, \{M_v\}_{v \in V}, \{\pi_v\}_{v \in V_{\text{LLM}}}, T)$ where:
- $V$ is a set of computation nodes
- $E \subseteq V \times V$ defines the control-flow graph
- $M_v$ is the implementation of node $v$ (an LLM module or a tool call)
- $\pi_v$ is the prompt parameter of LLM module $v \in V_{\text{LLM}}$
- $T$ is the set of available tools

The system's behavior on input $x$ is the result of executing the control-flow graph; optimization (e.g. via [[teleprompters]] or [[reflective-prompt-evolution]]) targets the joint prompt parameters $\pi = \{\pi_v\}_{v \in V_{\text{LLM}}}$.

## Key variants

- **Pipelines (linear)** — fixed sequential composition of modules (e.g. retriever → reranker → reader → answer extractor). Most common; easiest to test and optimize per-module.
- **Branching pipelines (conditional)** — control flow includes `if`/`switch` based on intermediate outputs (e.g. classifier routes to one of three downstream modules).
- **Iterative compounds** — modules invoked in a loop until a stopping condition (e.g. multi-hop retrieval: retrieve → reason → query reformulation → retrieve …).
- **Agentic compounds** — open-ended control flow where an LLM module decides which tool to call next (e.g. ReAct-style agents). Hardest to reason about and optimize.
- **Multi-LLM compounds** — different modules use different LLMs (e.g. cheap LLM for classification, frontier LLM for reasoning, distilled LLM for evaluation).

## Comparison

| Approach | Single LLM call | Compound AI system | End-to-end fine-tuned model |
|---|---|---|---|
| Modularity | None | High | None (monolithic) |
| Per-module testability | N/A | Yes | No |
| Per-module optimization | N/A | Yes (DSPy/GEPA) | No (joint training only) |
| Latency | Lowest | Higher (multiple calls) | Lowest |
| Cost | Lowest per task | Higher per task | Inference cheap, training expensive |
| Failure interpretability | Low | High (per-module) | Very low |
| Adaptation cost | Re-prompt | Re-optimize prompts | Re-train weights |

## When to use

- The task naturally decomposes into sub-skills (retrieve → reason → evaluate)
- Different sub-skills benefit from different models or different prompts
- Robustness and interpretability matter more than minimal latency
- Available training data is small (compound + prompt optimization is more sample-efficient than end-to-end training)
- The workflow needs to integrate non-LLM tools (search, code, calculators)

## Key papers

- [[dspy-compiling-declarative-language-model-calls]] (2023) — formalizes the compound AI system as a programmable abstraction (signatures + modules + teleprompters) with a compilation model
- [[gepa-reflective-prompt-evolution-outperform-reinforcement]] (2025) — extends compound-AI-system optimization with reflective evolution and Pareto-aware selection; demonstrates the paradigm at scale across heterogeneous benchmarks

> [!warning] Known limitations
> - Per-module optimization can produce locally optimal modules that are globally suboptimal (the joint optimum may differ from the composition of individual optima)
> - Module boundaries become rigid — refactoring the decomposition mid-project is expensive
> - Latency adds up linearly (or worse) with the number of LLM calls in the pipeline
> - Cost grows with module count; for very high-throughput applications a fine-tuned end-to-end model can be cheaper at inference
> - Agentic compounds (open-ended control flow) are much harder to optimize than fixed-pipeline compounds; results from DSPy/GEPA do not directly transfer
>
> *Source: LLM analysis*

> [!question] Open problems
> - Joint optimization across modules: when does sequential per-module optimization match joint optimization, and when does it fail?
> - Theoretical comparison: compound system + prompt optimization vs end-to-end fine-tuning at matched compute
> - Automatic decomposition: can the module structure itself be learned, or must it always be hand-designed?
> - Failure attribution in long compound systems — which module is "responsible" when the final answer is wrong?
> - Continual adaptation: how do compounds evolve as upstream LLMs change versions?
>
> *Source: LLM analysis*

> [!tip] My understanding
> Compound AI system is the right unit of analysis for production LLM applications. The shift from "model-centric" to "system-centric" framing (BAIR blog 2024) tracks where the field is going: proprietary moats are increasingly in the orchestration layer (which modules, what prompts, what data flow) rather than in the underlying LLM. The DSPy + GEPA combination — declarative compound + reflective optimization — is becoming the canonical stack.
>
> For the self-learning project, the entire Phase 1+2 architecture (Classifier → Prompt Refiner → Output Evaluator + regression check) *is* a compound AI system. Treating it explicitly as one — module-by-module signatures, optimizers, regression tests — is what makes Phase 2's automation tractable.
>
> *Source: LLM analysis*
