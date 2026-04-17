---
title: "Compiled DSPy programs outperform hand-crafted prompt chains"
slug: dspy-compiled-programs-outperform-hand-crafted-prompts
status: supported
confidence: 0.8
tags: [prompt-optimization, llm-programming, compilation, few-shot-learning]
domain: NLP
source_papers: [dspy-compiling-declarative-language-model-calls, gepa-reflective-prompt-evolution-outperform-reinforcement]
evidence:
  - source: dspy-compiling-declarative-language-model-calls
    type: supports
    strength: strong
    detail: "On GSM8K and HotPotQA, compiled DSPy programs beat standard few-shot prompting by >25% (GPT-3.5) and >65% (llama2-13b-chat); beat pipelines with expert-created demonstrations by 5-46% (GPT-3.5) and 16-40% (llama2-13b-chat); compiled programs on 770M T5 and llama2-13b-chat match hand-crafted prompts for proprietary GPT-3.5."
  - source: gepa-reflective-prompt-evolution-outperform-reinforcement
    type: supports
    strength: strong
    detail: "GEPA (a reflective-evolution prompt optimizer) beats vanilla and MIPROv2-compiled prompts on six tasks (HotPotQA, IFBench, HoVer, PUPA, AIME-2025, code-gen) across GPT-4.1 mini and Qwen3-8B, with GEPA+Merge giving +16% aggregate score over MIPROv2 on GPT-4.1 mini. Independent confirmation that compiled-prompt approaches dominate hand-crafted prompts, and demonstrates the gap holds with newer frontier models."
conditions: "Requires (a) a programmatically-scorable validation metric capturing task quality, (b) a small training set (tens to hundreds of labelled examples), (c) budget for offline compilation (multiple LLM calls per training example during bootstrapping). Benchmarks are closed-domain with verifiable answers; extension to open-ended or preference-based tasks is not directly tested. GEPA further establishes that the gap holds across newer frontier models (GPT-4.1, Qwen3) and across reflective-evolution as well as bootstrap-style optimizers."
date_proposed: 2026-04-13
date_updated: 2026-04-17
---

> [!abstract] Statement
> An LLM pipeline expressed as a DSPy program — modular signatures + teleprompter-compiled few-shot demonstrations — produces higher task quality than a functionally equivalent pipeline implemented with hand-crafted prompt templates, even when the hand-crafted prompts are written by experts.

## Evidence summary

**Supporting evidence:**
- **[[dspy-compiling-declarative-language-model-calls]]** (2023): on GSM8K math word problems, vanilla GPT-3.5 → 33%, compiled DSPy `ChainOfThought` with reflection → 82% (+49 pp); llama2-13b-chat 9% → 47%. On HotPotQA multi-hop QA, GPT-3.5 32% → 46%; llama2-13b-chat 22% → 41%. Compiled programs on 770M-parameter T5 and 13B llama2 are competitive with hand-crafted prompt chains for GPT-3.5 — meaningful cost/capability trade-off.
- **[[gepa-reflective-prompt-evolution-outperform-reinforcement]]** (2025): independent confirmation on six newer benchmarks (HotPotQA, IFBench, HoVer, PUPA, AIME-2025, CUDA/AIE code generation) and newer frontier models (GPT-4.1 mini, Qwen3-8B). GEPA — a reflective-evolution prompt optimizer in the same compiled-prompt paradigm — beats vanilla baselines and the prior leading optimizer MIPROv2 by >10% on average; GEPA+Merge gives +16% aggregate over MIPROv2 on GPT-4.1 mini. Same broader claim (automated compiled prompts > hand-crafted), different optimizer family.

> [!info] Conditions and scope
> The claim holds when:
> - The task has a programmatically-scorable validation metric (GSM8K: exact-match on numeric answer; HotPotQA: F1 on extracted span)
> - Training data (even 20-100 labelled examples) is available for bootstrapping
> - The pipeline decomposes cleanly into a handful of modules (1-5 in the case studies)
> - Compilation cost is acceptable (tens of minutes, dollars-to-tens-of-dollars in LLM calls for GPT-3.5)
>
> It does **not** directly address:
> - Open-ended tasks where quality is judged by preference rather than verifiable correctness
> - Very large pipelines (10+ modules) where compilation complexity may grow super-linearly
> - Online/continual optimisation as new data arrives
> - Robustness to distribution shift between compilation-time and deployment-time inputs

> [!warning] Counter-evidence
> None in the ingested literature as of this paper. Open question: does the gap close when experts have substantial iteration budget for hand-crafted prompts? The paper compares against a snapshot of expert prompts; a well-resourced prompt engineer iterating for days might narrow the gap — but the paper's point is that DSPy requires minutes not days.

## Linked ideas

This claim is the empirical foundation for the self-learning project's Phase 2 direction — Phase 1 data collection generates exactly the `(input, final_prompt, classification)` records DSPy teleprompters consume as training examples.

> [!question] Open questions
> - Does the advantage hold for preference-based tasks where the validation metric is itself an LLM judge?
> - Cross-model transfer: how much accuracy is lost if a program compiled for GPT-3.5 is deployed on Claude without re-compilation?
> - Optimal training-set size — diminishing returns beyond what N?
> - How does this interact with parameter-efficient fine-tuning (LoRA) at matched compute?
