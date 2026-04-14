---
title: "Chain-of-Thought Prompting"
slug: "chain-of-thought-prompting"
domain: "NLP"
status: mainstream
aliases: ["CoT", "chain-of-thought", "step-by-step reasoning"]
first_introduced: "2022"
date_updated: 2026-04-13
source_url: ""
---

## Definition

Chain-of-thought (CoT) prompting is a technique that elicits intermediate reasoning steps from a language model before it produces a final answer. By decomposing complex problems into sequential reasoning traces, CoT improves accuracy on tasks requiring multi-step inference, arithmetic, or logical deduction.

> [!tip] Intuition
> Instead of asking a model to jump directly from question to answer, CoT prompting asks it to "think step by step" — producing a visible reasoning chain that both improves accuracy and makes the model's logic inspectable. The intermediate steps serve as a scratchpad where the model can accumulate partial results before committing to a final output.
>
> *Source: LLM analysis*

## Formal notation

Standard prompting: $x \rightarrow y$

Chain-of-thought prompting: $x \rightarrow r_1, r_2, \ldots, r_n, y$

where $r_i$ are intermediate reasoning steps. In DSPy, `dspy.ChainOfThought` implements this by expanding a signature `question -> answer` into `question -> rationale, answer`, prepending the instruction "Reasoning: Let's think step by step." to the rationale field.

## Key variants

- **Zero-shot CoT**: appending "Let's think step by step" without demonstrations
- **Few-shot CoT**: providing demonstrations that include reasoning traces
- **Self-consistency**: sampling multiple CoT paths and selecting the majority answer
- **Tree-of-thought**: branching reasoning into parallel exploration paths
- **Program-of-thought**: generating executable code as the reasoning trace
- **Scratchpad**: free-form intermediate working space before final output

> [!warning] Known limitations
> - Increases token usage significantly (reasoning traces are verbose)
> - Reasoning traces can be unfaithful — the stated chain may not reflect the model's actual computation
> - Less effective on simple tasks where direct answers suffice
> - Self-generated rationales can reinforce incorrect reasoning patterns
>
> *Source: LLM analysis*

> [!question] Open problems
> - Verifying faithfulness of reasoning traces
> - Optimal granularity of reasoning steps for different task types
> - Automated discovery of effective CoT patterns via bootstrapping
>
> *Source: LLM analysis*

> [!info] Relevance to active research
> In DSPy, `ChainOfThought` is a drop-in replacement for `Predict` that automatically adds a rationale field. During bootstrapping, the system can self-generate rationales for training examples that only have final labels — enabling the optimizer to learn effective reasoning patterns. The self-learning project's Classifier steps and Prompt Refiner could potentially benefit from CoT to produce more transparent, reviewable reasoning that developers can inspect.
>
> *Source: LLM analysis*
