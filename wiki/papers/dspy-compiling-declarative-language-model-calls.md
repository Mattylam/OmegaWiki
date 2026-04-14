---
title: "DSPy: Compiling Declarative Language Model Calls into Self-Improving Pipelines"
slug: dspy-compiling-declarative-language-model-calls
arxiv: "2310.03714"
venue: "arXiv (cs.CL) → ICLR 2024"
year: 2023
tags: [prompt-optimization, llm-programming, declarative-programming, llm-pipelines, bootstrapping, self-improving]
importance: 5
date_added: 2026-04-13
source_type: pdf
s2_id: ""
keywords: [DSPy, signatures, modules, teleprompters, bootstrapping, compiler, declarative prompting, self-improving pipelines]
domain: NLP
code_url: "https://github.com/stanfordnlp/dspy"
cited_by: []
---

## Problem

Existing LLM pipelines are implemented using hard-coded "prompt templates" — lengthy strings of instructions and demonstrations crafted through manual trial and error. This approach, while pervasive, is brittle and unscalable: a given string prompt may not generalise across different pipelines, LMs, data domains, or even individual inputs within the same task. Popular frameworks (LangChain, LlamaIndex, Semantic Kernel) inherit this limitation because they also express task-specific behaviour through hand-written prompt templates. The result is that building a new LM pipeline requires artisanal string manipulation conceptually akin to hand-tuning the weights of a classifier. What's needed is a programming model that abstracts LM invocation the way PyTorch abstracts neural-network layers — letting the compiler handle the fragile prompt-construction work.

## Key idea

Replace hand-crafted prompts with a three-layer abstraction:

1. **Signatures** — declarative natural-language typed function specs (e.g. `question -> answer`) that tell DSPy *what* a text transformation needs to do, without prescribing *how* the LM should be prompted
2. **Modules** — parameterised, composable units (Predict, ChainOfThought, ReAct, ProgramOfThought) that implement signatures; any module can be used interchangeably wherever a signature is expected, like layers in PyTorch
3. **Teleprompters** — general-purpose optimisers that compile a DSPy program over a training set by bootstrapping demonstrations and selecting the highest-scoring configuration

A DSPy compiler takes a program, training inputs (with optional labels), and a validation metric, then produces an optimised version via bootstrapping — simulating the program on inputs, collecting successful execution traces, and injecting them as few-shot demonstrations or using them to fine-tune small LMs. The central move: reframe prompt engineering as a compilation problem over a modular computation graph.

## Method

**Programming model** (Sec 3):
- A signature is a tuple of input fields and output fields, optionally with an instruction. Field names carry semantic role information the compiler exploits.
- Modules are callable functions implementing a signature. Internally: `Predict` stores the signature, an optional LM override, and a list of demonstrations; it formats a prompt, calls the LM, and parses the output.
- `ChainOfThought` automatically expands a signature `question -> answer` into `question -> rationale, answer`, prepending "Reasoning: Let's think step by step." to the rationale field.
- Pipelines are built as define-by-run computation graphs using standard Python control flow — `if`, `for`, exceptions — with modules as nodes.

**Compiler** (Sec 4):
- **Teleprompters** are general-purpose optimisation strategies independent of the pipeline. They generate candidate parameters (instructions, demonstrations), evaluate them against a metric, and keep the best configuration.
- **BootstrapFewShot** simulates the program on training inputs, collects execution traces for runs that pass the metric, and selects successful traces as few-shot demonstrations. Key property: can generate missing labels for intermediate pipeline steps (rationales, search queries) even when only the final answer has a label.
- **BootstrapFewShotWithRandomSearch** runs multiple random configurations and keeps the best.
- **LabeledFewShot** directly injects labelled examples without bootstrapping — no LLM calls during compilation.
- **BootstrapFinetune** uses bootstrapped demonstrations to fine-tune a smaller LM (e.g. T5-770M or Llama2-13b-chat) rather than just selecting prompts.

**Bootstrapping mechanics**: the compiler runs the program on a small training set, traces every module invocation, and keeps only traces from runs whose final output passes the metric. Successful intermediate steps become the few-shot demonstrations injected back into prompts.

## Results

Two case studies:

**GSM8K (math word problems) with GPT-3.5:**
- Vanilla prompt: 33%
- DSPy-compiled `ChainOfThought + reflection`: **82%** (+49 pp)
- llama2-13b-chat: 9% → 47% (+38 pp)

**HotPotQA (multi-hop QA) with GPT-3.5:**
- Vanilla: 32%
- DSPy-compiled multi-hop retrieval + reasoning: **46%** (+14 pp)
- llama2-13b-chat: 22% → 41% (+19 pp)

**Quantitative summary**: compiled DSPy programs outperform standard few-shot prompting by generally over 25% (GPT-3.5) and 65% (llama2-13b-chat). They beat pipelines with expert-created demonstrations by 5–46% (GPT-3.5) and 16–40% (llama2-13b-chat). Compiled programs on 770M-parameter T5 and llama2-13b-chat are competitive with hand-crafted prompt chains for proprietary GPT-3.5.

> [!warning] Limitations
> - Compilation is offline and requires a validation metric that captures task quality — for tasks where quality is subjective or preference-based (not the case for GSM8K / HotPotQA) this is a bottleneck
> - Gains depend on the availability of at least a small labelled training set (tens to hundreds of examples)
> - The compiler re-runs the program many times during compilation — can be expensive in LLM calls for large programs or expensive base models
> - Teleprompter choice matters: BootstrapFewShot ≠ MIPROv2 ≠ COPRO; paper does not give principled guidance for when to use which
> - Results are model-specific: a program compiled for GPT-3.5 may not transfer cleanly to Claude or open models without re-compilation
> - Abstract program structure still requires developer judgement — DSPy automates prompt optimisation, not pipeline architecture

> [!question] Open questions
> - Optimal teleprompter selection as a function of task, model, and data budget
> - Cross-model transfer of compiled programs without full re-compilation
> - Compilation under noisy or adversarial validation signals (relevant to preference-based domains)
> - Principled combination of prompt optimisation with parameter-efficient fine-tuning
> - Scaling to pipelines with 10+ modules — does compilation complexity grow super-linearly?

> [!tip] My take
> This is the canonical paper for the self-learning project's Phase 2 direction. Phase 1 collects exactly the training signal DSPy compilers need: `(feedback, original_prompt, final_prompt, classification, resolution_score)` records map directly onto DSPy `Example` objects, where `feedback` + `original_prompt` are inputs, `final_prompt` / `classification` are labels, and the developer's approve/reject is the validation metric. The three-module structure of the self-learning pipeline (Classifier, Prompt Refiner, Output Evaluator) is already DSPy-shaped — each is independently optimisable. Recommended path: implement the Classifier's three sub-steps as DSPy signatures first (since the user's note already confirms DSPy is being considered for this), use LabeledFewShot once there are 5–10 developer-confirmed records per step, and promote to BootstrapFewShot once 20–50 records per step are available. The Prompt Refiner is harder because its "correctness" signal comes from the downstream Output Evaluator + regression check, not from direct labels — that's a case for BootstrapFewShot using the full pipeline's pass/fail signal as the metric.

## Related

- supports: [[dspy-compiled-programs-outperform-hand-crafted-prompts]]
- supports: [[bootstrapping-produces-better-demonstrations-than-hand-written]]
- [[dspy-signatures]]
- [[teleprompters]]
- [[bootstrap-dspy]]
- Authors: [[omar-khattab]], [[christopher-potts]], [[matei-zaharia]]
- [[prompt-engineering]]
- [[prompt-optimization]]
- [[few-shot-prompting]]
- [[chain-of-thought-prompting]]
- [[in-context-learning]]
- [[react-prompting]]
- [[scratchpad]]
- [[fine-tuning]]
- [[brittle]]
