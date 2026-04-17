---
title: "Teleprompters"
aliases: ["teleprompter", "DSPy optimizer", "DSPy compiler strategy", "LM pipeline optimizer"]
tags: [prompt-optimization, compilation, llm-programming, dspy]
maturity: emerging
key_papers: [dspy-compiling-declarative-language-model-calls, gepa-reflective-prompt-evolution-outperform-reinforcement]
first_introduced: "2023"
date_updated: 2026-04-17
related_concepts: [reflective-prompt-evolution, compound-ai-system]
---

## Definition

A **teleprompter** is a general-purpose optimisation strategy in the DSPy programming model that takes a program, a training set, and a validation metric, and returns a new optimised program. The name is derived from "abstracting and automating the task of prompting" — it happens at a distance, without manual intervention. Teleprompters are highly modular: the same optimisation strategy applies to different types of pipelines, determining how their internal modules should learn from data.

> [!tip] Intuition
> A teleprompter is to a DSPy program what an optimiser (SGD, Adam) is to a neural network — a general strategy that improves a program's parameters from data. But where neural-network optimisers adjust weights, a teleprompter adjusts prompt-level parameters: few-shot demonstrations, instruction strings, or chain-of-thought rationales. The output of a teleprompter is not a new model; it is a new DSPy program whose modules have been filled in with compiler-discovered demonstrations.

## Formal notation

Given a DSPy program $P$ with modules $\{M_i\}$, a training set $D = \{(x_j, y_j)\}$, and a validation metric $m: (x, y) \to [0, 1]$, a teleprompter $T$ produces an optimised program:

$$P^* = T(P, D, m) = \arg\max_{P' \in \mathcal{P}(P)} \frac{1}{|D_{\text{val}}|} \sum_{(x, y) \in D_{\text{val}}} m(P'(x), y)$$

where $\mathcal{P}(P)$ is the space of programs reachable from $P$ by varying module parameters (instructions, demonstrations). The optimisation typically proceeds in three stages: candidate generation (generate parameter candidates per module), parameter optimisation (select best candidates via random search / cross-validation / Bayesian hyperparameter search), and optional higher-order optimisation (e.g. ensembling multiple compiled programs).

## Variants

- **`LabeledFewShot(k)`**: the simplest teleprompter. Samples $k$ random demonstrations from the training set and injects them as few-shot examples. No LLM calls at compile time.
- **`BootstrapFewShot`**: simulates the program on training inputs, collects execution traces, filters by metric, and selects successful traces as few-shot demonstrations. Bootstraps missing labels for intermediate pipeline steps (e.g. rationales, search queries) even when only end-to-end labels exist.
- **`BootstrapFewShotWithRandomSearch`**: runs BootstrapFewShot with multiple random configurations (seed, demo count) and keeps the best-performing program.
- **`BootstrapFinetune`**: uses bootstrapped demonstrations to fine-tune a smaller LM (e.g. T5, llama2-13b) rather than selecting prompts. The optimisation target shifts from prompt text to model weights.
- **`COPRO`** (later paper): rewrites instruction strings in signatures using an LLM-as-optimiser.
- **`MIPROv2`** (later paper): jointly optimises instructions and few-shot examples; most powerful but most data-hungry.
- **`GEPA` / `GEPA+Merge`** ([[gepa-reflective-prompt-evolution-outperform-reinforcement]], 2025): [[reflective-prompt-evolution|reflective evolutionary]] teleprompter — uses an LLM to reflect on sampled trajectories and propose prompt updates, with Pareto-front candidate selection and optional system-aware merge crossover for multi-module systems. Beats MIPROv2 by >10% across six tasks and matches or beats GRPO RL with up to 35× fewer rollouts.

## Comparison

| Teleprompter | What it optimises | Training data needed | LLM calls at compile |
|---|---|---|---|
| LabeledFewShot | Few-shot demo selection | ~3–10 labelled examples | 0 |
| BootstrapFewShot | Bootstrapped few-shot demos | ~20–50 labelled examples | Moderate (one per training input) |
| BootstrapFewShotWithRandomSearch | Same, with tuning | ~30–100 | High |
| BootstrapFinetune | Small-model weights | ~50+ | High (also fine-tuning compute) |
| COPRO | Instruction strings | ~10–20 | Moderate |
| MIPROv2 | Joint instructions + demos | ~50–100 | Very high |
| GEPA / GEPA+Merge | Per-module instructions via reflective evolution | ~50–200 (Pareto pool) | Very high (reflection LLM dominates) |

## When to use

- **LabeledFewShot**: fastest start; baseline to establish before investing in more complex teleprompters
- **BootstrapFewShot**: default choice once ~20 labelled examples are available and baseline program has >0 success rate
- **BootstrapFinetune**: when production cost or latency demands a smaller model
- **COPRO / MIPROv2**: when prompt-level tuning plateaus on simpler teleprompters
- **GEPA / GEPA+Merge**: when MIPROv2 plateaus, when the validation distribution is heterogeneous (multi-task or multi-criterion), or when you have a multi-module compound system (merge crossover earns its keep)

> [!warning] Known limitations
> - Compile-time cost can be significant — BootstrapFewShot needs to run the program on every training input at least once
> - Gains are brittle to the validation metric; a noisy metric produces noisy teleprompter output
> - No principled guidance on which teleprompter to use when — the paper leaves this to empirical trial
> - Teleprompters optimise for training-distribution performance; deployment shift can erode gains
> - Optimised programs are model-specific; switching LMs generally requires re-compilation

> [!question] Open problems
> - Automatic teleprompter selection as a function of task, model, data budget
> - Online teleprompters that continue optimising as production data arrives
> - Robustness under adversarial or preference-based validation signals
> - Theoretical guarantees for teleprompter convergence (existing work is empirical)

## Key papers

- [[dspy-compiling-declarative-language-model-calls]] (2023) — introduces the teleprompter abstraction and the LabeledFewShot, BootstrapFewShot, BootstrapFewShotWithRandomSearch, BootstrapFinetune variants
- [[gepa-reflective-prompt-evolution-outperform-reinforcement]] (2025) — adds GEPA and GEPA+Merge teleprompters: reflective evolution + Pareto-front selection + system-aware merge crossover. Establishes that prompt-space optimization can outperform RL post-training under bounded rollout budgets.

> [!tip] My understanding
> Teleprompters are the mechanism that makes "compile your LLM program" a literal statement rather than a metaphor. The key abstraction move is: optimisation strategy is *decoupled* from the specific pipeline being optimised. For the self-learning project, this means a single teleprompter (likely BootstrapFewShot) can be applied independently to each of the Classifier's three steps once each has ~20-50 developer-confirmed records. Developer-override records are particularly valuable as mandatory few-shot seeds (the paper's framing: "examples that actually caused the model to produce correct outputs" — and the developer correction is precisely that signal).
