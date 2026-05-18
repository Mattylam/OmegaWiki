---
title: "GEPA: Reflective Prompt Evolution Can Outperform Reinforcement Learning"
slug: gepa-reflective-prompt-evolution-outperform-reinforcement
arxiv: "2507.19457"
venue: "arXiv (cs.CL/cs.LG/cs.AI/cs.SE)"
year: 2025
tags: [prompt-optimization, compound-ai-system, reflective-evolution, sample-efficiency, reinforcement-learning, evolutionary-search, pareto-selection, dspy]
importance: 5
date_added: 2026-04-17
source_type: pdf
s2_id: "f1c0aad419cd942d5a135b47fcce58d101dbb3bd"
keywords: [reflective prompt evolution, multi-objective evolutionary search, Pareto front optimization, language-based feedback loops, compound AI system optimization, GRPO, MIPROv2]
domain: NLP
code_url: "https://github.com/gepa-ai/gepa"
cited_by: []
---

## Problem

LLMs are increasingly adapted to downstream tasks via reinforcement learning methods such as Group Relative Policy Optimization (GRPO), which typically require **thousands of rollouts** to learn each new task. This sample-inefficiency stems from the fact that policy gradients are computed from sparse, scalar reward signals — every successful or failed trial yields only a single bit (or bounded scalar) of learning signal. The result is that adapting a [[compound-ai-system]] (a multi-module pipeline of prompted LLMs and tools) to a new task requires expensive RL fine-tuning loops, often consuming tens of thousands of LLM calls before convergence.

The authors argue that LLMs themselves can extract a much richer signal from each rollout when allowed to read and reason about the **trajectory in natural language** — the chain of reasoning steps, tool invocations, and intermediate outputs — rather than reducing each trial to a single reward number. Yet existing prompt optimizers (MIPROv2, COPRO, [[teleprompters]]) do not systematically exploit this trajectory-level reflection signal, and existing evolutionary prompt searches lack mechanisms to combine complementary lessons from a diverse pool of attempts without collapsing to local optima.

## Key idea

GEPA (**G**enetic-**P**areto) is a prompt optimizer that combines three ideas:

1. **Reflective prompt mutation** — given a sampled trajectory (input → reasoning → tool call → output → metric), an LLM is prompted to *reflect* in natural language on what went wrong / right and propose a concrete prompt update that addresses the diagnosed issue. This converts a scalar reward into a paragraph of structured diagnosis.
2. **Pareto-based candidate selection** — instead of selecting parents purely by aggregate score, GEPA maintains the Pareto frontier across a multi-objective trade-off (per-task / per-criterion performance), so that prompts with complementary strengths are retained as parents even when no single prompt dominates. This prevents collapse to local optima that overfit to one subset of validation tasks.
3. **System-aware merging (crossover)** — for [[compound-ai-system]]s with multiple prompted modules, GEPA can merge complementary lessons by taking the best per-module prompt across two parents on the Pareto frontier, producing a child that inherits strengths from both.

The whole loop is *sample-efficient by construction*: each trajectory carries a paragraph of diagnostic content, and Pareto selection extracts every drop of diversity from the pool, so even a few rollouts can drive large quality gains.

## Method

### Compound AI system (target of optimization)

A compound AI system is a control-flow program with one or more prompted LLM modules and tools (search, retrieval, code execution). Each module has a prompt parameter $\pi_i$. GEPA optimizes the joint configuration $\pi = (\pi_1, \ldots, \pi_k)$ to maximize a task metric $m$ on a held-in validation set.

### GEPA loop

For up to $B$ total rollout budget:

1. **Sample** a small batch of validation tasks; run the current best-candidate program; collect trajectories (reasoning, tool calls, outputs, metric scores).
2. **Reflect**: for each trajectory (esp. failures), call a reflection LLM with the meta-prompt (Appendix B of paper) that asks it to diagnose the failure and propose an updated prompt $\pi_i'$ for the responsible module.
3. **Evaluate** the candidate $\pi'$ on a small minibatch; if it beats the parent on those tasks, add it to the candidate pool.
4. **Pareto front update**: maintain the Pareto frontier of candidates over per-task scores (each task is an objective); selection probability for the next parent is biased toward Pareto-front candidates.
5. **Optional merge**: with some probability, sample two Pareto-front candidates and produce a child by per-module crossover (system-aware merging).

### Reflection meta-prompt

The reflection LLM receives the current prompt, a sample of trajectories with input/output/metric, and is instructed to (a) identify the dominant failure mode, (b) propose a refined prompt that addresses it, (c) preserve any working behavior, (d) integrate domain-specific knowledge from the trajectories. Appendix B of the paper gives the exact meta-prompt template.

### Variants studied

- **GEPA** — base reflective evolution + Pareto selection (no merge)
- **GEPA+Merge** — adds system-aware crossover; produces best results, especially on multi-module systems (HotPotQA, HoVer)

### Baselines

- **GRPO** — Group Relative Policy Optimization, recent RL post-training method (DeepSeek)
- **MIPROv2** — leading prior prompt optimizer; jointly optimizes instructions and few-shot examples
- **Vanilla** — base LLM without optimization

### Models

GPT-4.1 mini and Qwen3-8B as student/agent LLMs; GPT-4o as reflection LLM in some configurations.

## Results

Across **six tasks** (HotPotQA, IFBench, HoVer, PUPA, AIME-2025, plus a code-generation benchmark for inference-time search):

| Comparison | Average gain | Best case | Rollout reduction |
|---|---|---|---|
| GEPA vs GRPO | +6% | +20% | up to **35×** fewer rollouts |
| GEPA vs MIPROv2 | +10% | +12% (AIME-2025) | comparable |
| GEPA+Merge vs MIPROv2 (GPT-4.1 mini, aggregate) | +16% | — | comparable |

Additional findings:

- **Inference-time search**: GEPA used as an inference-time prompt-search procedure for CUDA/AIE kernel code generation produced significantly faster kernels than zero-shot GPT-4o on both AMD and NVIDIA hardware (Section 6, Appendix J).
- **Generalization gap (Appendix D)**: prompts evolved by GEPA generalize to held-out test sets better than few-shot exemplars optimized by MIPROv2 — the authors attribute this to advanced LLMs' improved instruction-following meaning instruction-style prompts beat exemplar-style prompts.
- **Prompt size**: GEPA+Merge produces shorter prompts than MIPROv2 because it prioritizes instructional content over few-shot examples (Appendix F).

## Method illustration (Appendix G — PUPA case study)

The privacy-preserving query rewriting prompt for PUPA evolves over GEPA iterations from a 2-line "rewrite this query without sensitive info" instruction to a multi-section structured prompt with: (1) explicit identification rules for sensitive data categories, (2) a step-by-step generalization procedure, (3) a justification requirement explaining how privacy is preserved, (4) format constraints for the downstream LLM. The evolution is fully driven by the reflection LLM's diagnoses of failure cases — no human edits.

> [!warning] Limitations
> - **Prompt-vs-weight learning boundary unclear**: GEPA outperforms RL on these tasks but the authors do not characterize *which* tasks favor prompts (instructional, low-rollout regimes) vs weights (perceptual, high-rollout regimes). Practitioners must empirically choose.
> - **Hyperparameter sensitivity**: minibatch size, mutation count per iteration, merge probability, and reflection-LLM choice all materially affect results; no principled tuning protocol is offered.
> - **Reflection-LLM dependence**: GEPA's quality is bounded by the reflection LLM's diagnostic ability. Using a weaker reflection LLM can stall optimization. Cost concern: the reflection LLM is often a frontier model called many times.
> - **Sample efficiency claim is comparative, not absolute**: 35× fewer rollouts than GRPO still means hundreds of rollouts for harder tasks. Wall-clock time can be high if reflection calls dominate.
> - **Exemplar optimization not explored**: GEPA evolves instructions but does not explicitly optimize few-shot examples (which MIPROv2 does); a hybrid is hinted at as future work.
> - **Six benchmarks is narrow**: while diverse (QA, math, instruction-following, privacy, code), all are short-horizon tasks with clear metrics. Behavior on long-horizon agentic tasks (multi-day workflows, open-ended research) is untested.
> - **Pareto-front size grows**: maintaining the frontier costs memory; the paper does not report frontier-size scaling for very long optimization runs.

> [!question] Open questions
> - When does prompt-space optimization fundamentally hit a ceiling that requires weight updates? (boundary characterization)
> - Can the reflection LLM be the same as the student LLM (self-reflection) without quality collapse?
> - How does GEPA compose with weight-space adaptation — does prompt-then-fine-tune outperform fine-tune-then-prompt?
> - What is the right exploration / exploitation balance in Pareto-aware evolutionary search? Is there a theoretical analysis?
> - Cross-task transfer: do GEPA-evolved prompts on task A generalize to related task B without re-optimization?

> [!tip] My take
> The headline result — **prompt evolution beating RL with 35× fewer rollouts** — is significant because it reframes the post-training stack: for tasks where the underlying capability is present but the *invocation pattern* is wrong, you should not be doing GRPO. The empirical wins on AIME-2025 (a math benchmark, where the conventional wisdom favors RL with verifiable rewards) are particularly striking and probably the strongest evidence in the paper.
>
> The Pareto-selection idea is the under-discussed contribution. Plain evolutionary prompt search has been tried before (PromptBreeder, EvoPrompt) and consistently collapses; the Pareto frontier is the missing piece that keeps complementary lessons alive across iterations. This is a reusable algorithmic primitive — I'd expect it to show up in inference-time search and agent self-improvement work soon.
>
> Caveats: the comparison to GRPO is favorable partly because GRPO at low rollout budgets is a strawman — GRPO was designed for the high-rollout regime. The honest claim is that *prompt-space methods dominate at low rollout budgets, RL dominates at very high ones*, and GEPA is the right point estimate of how good prompt-space can get. The paper's framing slightly oversells.
>
> Practical implication: for the self-learning project's Phase 2, GEPA replaces MIPROv2 as the default optimizer once we have ≥50 developer-confirmed records per module. The reflection LLM can be the production model itself initially; we may want a stronger reflector once we hit MIPROv2-level plateaus.
>
> *Source: LLM analysis*

## Related

- supports: [[reflective-prompt-evolution-outperforms-reinforcement-learning]]
- supports: [[pareto-aware-candidate-selection-prevents-prompt]]
- supports: [[dspy-compiled-programs-outperform-hand-crafted-prompts]]
- [[reflective-prompt-evolution]]
- [[compound-ai-system]]
- [[teleprompters]]
- derived_from: [[prompt-optimization]]
- [[prompt-engineering]]
- [[chain-of-thought-prompting]]
- [[in-context-learning]]
- [[fine-tuning]]
- Authors: [[lakshya-agrawal]], [[omar-khattab]], [[christopher-potts]], [[matei-zaharia]]
- Related work: [[dspy-compiling-declarative-language-model-calls]] (DSPy framework GEPA integrates with)
- Orthogonal axis: [[system-prompt-optimization-meta-learning]] (MetaSPO — meta-learns task-agnostic system prompts across tasks; GEPA reflects across iterations within one task; combination unexplored)
- Orthogonal axis: [[task-facet-learning-structured-approach-prompt]] (UniPrompt — aggregates feedback across input clusters within one task to produce sectioned prompts; GEPA aggregates across optimization iterations on a Pareto frontier)
