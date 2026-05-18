---
title: "System Prompt Optimization with Meta-Learning"
slug: system-prompt-optimization-meta-learning
arxiv: "2505.09666"
venue: "NeurIPS 2025"
year: 2025
tags: [prompt-optimization, meta-learning, bilevel-optimization, llm, system-prompt, cross-task-transfer]
importance: 4
date_added: 2026-04-17
source_type: pdf
s2_id: "aa5207c5ea9546cb31873ed3fcdc58214708bd53"
keywords: [bilevel optimization, meta-learning, system prompt optimization, prompt generalization, test-time adaptation]
domain: NLP
code_url: "https://github.com/Dozi01/MetaSPO"
cited_by: []
---

## Problem

Existing automated [[prompt-optimization]] work focuses almost exclusively on the **user prompt** — the task-specific instruction tied to a single query distribution or benchmark. The **system prompt** — task-agnostic instructions intended to apply across many user prompts and domains — is left largely manual or unoptimized, despite being the more transferable and reusable artefact.

The paper formalizes this as a previously-unaddressed problem: how do you optimize a system prompt that is (a) robust across diverse user prompts within a task and (b) transferable to entirely unseen tasks?

## Key idea

Frame system prompt optimization as a **bilevel problem**:

- **Outer loop (meta-level)**: optimize the *system prompt* over a distribution of tasks, treating each task's optimized user prompt as a function of the current system prompt.
- **Inner loop (task-level)**: for each task in the meta-batch, optimize the *user prompt* given the current system prompt.

The two loops alternate, so the system prompt is shaped specifically to *enable* good per-task user-prompt adaptation, not just to score well on a fixed user prompt. This is direct meta-learning analogue of MAML applied at the prompt level — the system prompt plays the role of the "good initialization."

## Method

MetaSPO (Meta-System-Prompt Optimization):

1. Maintain a current system prompt `s_t` and per-task user prompts `{u_i}`.
2. **Inner step**: for each source task `T_i`, run a textual prompt optimizer (LLM-as-optimizer style) to update `u_i` against `(s_t, u_i)` evaluations.
3. **Outer step**: aggregate task-level signals (performance, gradient-style textual feedback) across all source tasks and update `s_t → s_{t+1}` via a meta-prompt that asks an optimizer LLM to revise the system prompt to better support the inner-loop adaptations.
4. Iterate until the system prompt stabilizes.

At test time, the frozen meta-learned `s*` is given to an unseen task and either (a) used directly (zero-shot transfer) or (b) used as the initialization for a few inner-loop user-prompt adaptation steps (test-time adaptation).

The optimizer is itself an LLM (no gradient access required), placing this in the same family as [[reflective-prompt-evolution]] and other natural-language-feedback optimizers, but operating at a *higher abstraction level* (across tasks rather than within one task).

## Results

Evaluated on **14 unseen datasets across 5 domains** (Medical, Review Analysis, Reasoning, Safety, Grounding):

- Average performance **44.5** across domains, beating user-prompt-only optimization baselines.
- Per-domain absolute gains: **+5–15% accuracy** (Medical, Review, Reasoning), **+8–12% F1** (Safety), **+7–10% EM** (Grounding).
- **Test-time adaptation** with the meta-learned system prompt requires **fewer optimization steps** than starting from scratch and reaches higher final performance — i.e. the meta-learned prompt is a useful *initialization*.
- Robustness holds across varying source-target task similarity, suggesting the optimized system prompt captures genuinely task-agnostic structure rather than overfitting source domains.

> [!warning] Limitations
> - **Optimizer LLM dependence**: the meta-loop relies on a strong LLM-as-optimizer; performance degrades with weaker optimizers (typical for the whole text-feedback prompt-optimization family — same limitation as [[reflective-prompt-evolution]]).
> - **Source-task curation cost**: meta-learning needs a diverse, labeled distribution of source tasks. Constructing this is non-trivial and the paper does not study how source-task choice biases the resulting system prompt.
> - **Single-loop comparison only**: baselines are user-prompt-only optimizers; the paper does not directly compare to other cross-task prompt approaches that use different aggregation mechanisms (e.g. evolutionary across-task pooling).
> - **Safety risk surface enlarged**: a transferable system prompt is a high-leverage artefact — adversarial perturbations or misuse propagate across all downstream tasks. The paper acknowledges this only briefly.

> [!question] Open questions
> - How does the meta-learning benefit scale with the **number and diversity of source tasks**? The paper uses 5 domains; behavior at 50 or 500 is unclear.
> - Can the bilevel formulation be applied to **streaming / online aggregation** of feedback from live deployments, rather than offline meta-training over a curated corpus?
> - How does MetaSPO compose with **trajectory-level reflection** ([[reflective-prompt-evolution]])? They operate at orthogonal levels — across tasks vs. across iterations on one task — and pooling both signals is unexplored.
> - What is the **right abstraction level** for the system prompt? The paper treats it as natural-language text; structured/decomposable system prompts (rules + skills) might generalize differently.

> [!tip] My take
> This is the cleanest formulation I've seen of cross-task prompt optimization as meta-learning. The bilevel framing is right — it's the prompt analogue of MAML — and it makes a real conceptual contribution beyond just "we ran a prompt optimizer on more tasks."
>
> The biggest gap is that the meta-loop is **offline**: it assumes a curated source-task distribution. The genuinely interesting frontier is **online aggregation across deployments** — taking textual feedback from many independent user/agent traces in production and distilling it into a shared system prompt without ever assembling a benchmark. MetaSPO is a strong anchor prior work for that direction but does not solve it.
>
> Also notable: this is orthogonal to [[gepa-reflective-prompt-evolution-outperform-reinforcement|GEPA]]. GEPA reflects across iterations within one optimisation problem; MetaSPO meta-learns across tasks. Combining them — reflective trajectory pooling at the meta level — looks like a clear follow-up.

## Related

- [[prompt-optimization]] — foundation; this paper extends prompt optimization from user-prompt-only to system+user bilevel
- [[meta-prompt-optimization]] — the concept this paper introduces / anchors
- supports: [[meta-learned-system-prompts-transfer-unseen]]
- [[gepa-reflective-prompt-evolution-outperform-reinforcement]] — orthogonal axis (within-task reflection vs cross-task meta-learning)
- [[reflective-prompt-evolution]] — adjacent prompt-optimization concept; MetaSPO operates at a higher abstraction level
- [[sectioned-prompt-optimization]] — sister concept (within-task across-cluster aggregation); orthogonal axis to MetaSPO's cross-task aggregation
- [[task-facet-learning-structured-approach-prompt]] — sister work (UniPrompt) on a different aggregation axis; composing MetaSPO's cross-task meta-learning with UniPrompt's sectioned structure is an open follow-up
- [[sung-ju-hwang]]
