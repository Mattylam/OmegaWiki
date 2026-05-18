---
title: "Taming Actor-Observer Asymmetry in Agents via Dialectical Alignment"
slug: taming-actor-observer-asymmetry-agents-dialectical
arxiv: "2604.19548"
venue: ""
year: 2026
tags: [multi-agent, cognitive-bias, fault-attribution, dialectical-reasoning, rl-alignment, actor-observer-asymmetry, grpo, llm-agents, perspective-invariant-reasoning]
importance: 3
date_added: 2026-05-01
source_type: pdf
s2_id: "f03915e5a8e9ea621d4031df34edc083422dcf50"
keywords: [actor-observer asymmetry, dialectical reasoning, agent collaboration, fault attribution, perspective-invariant reasoning]
domain: NLP
code_url: ""
cited_by: []
---

## Problem

Multi-agent LLM systems that assign specialized roles (actor for self-reflection, observer for mutual auditing) suffer from a systematic cognitive bias: the same agent produces inconsistent failure attributions depending on which role it occupies. Actors tend to attribute failures to external factors (task ambiguity, tool errors), while observers attribute the same failures to internal faults (reasoning errors, wrong decisions). This **Actor-Observer Asymmetry (AOA)** destabilizes fault diagnosis pipelines and produces conflicting audit signals across rounds.

## Key idea

Quantify AOA using a counterfactual benchmark (Ambiguous Failure Benchmark, AFB), then train a model — **ReTAS** (Reasoning via Thesis-Antithesis-Synthesis) — that explicitly synthesizes conflicting actor and observer perspectives into a single evidence-grounded attribution. The training signal comes from Group Relative Policy Optimization (GRPO) over dialectical chain-of-thought trajectories, enforcing perspective invariance as a learning objective rather than relying on architectural role separation alone.

## Method

**Ambiguous Failure Benchmark (AFB)**: Paired counterfactual failure scenarios across 10 domains where the same incident can plausibly be attributed either internally or externally. Models are probed twice — once in actor role, once in observer role — and the flip rate measures AOA severity.

**ReTAS training pipeline**:
1. *Attribution phase*: retrieve retrieval-augmented context to identify plausible internal/external causes for a failure
2. *Dialectical CoT synthesis*: generate trajectories with three stages — Thesis (actor-role attribution), Antithesis (observer-role counter-attribution), Synthesis (evidence-arbitrated consensus)
3. *GRPO alignment*: optimize over groups of sampled trajectories using reward combining attribution consistency and fault resolution accuracy

**Key design choice**: dialectical CoT is generated synthetically from the two role perspectives, then used as supervised signal for the synthesis stage. This means perspective-invariance is taught as a reasoning skill rather than enforced by post-hoc debiasing.

## Results

- Perspective-swap triggers AOA in >20% of cases for most LLMs tested on AFB
- ReTAS (4B params) outperforms larger baselines on FinQA-TAS and Spider-TAS on attribution accuracy and flip score
- Reduces V-AOA metric significantly; achieves superior parameter efficiency
- Ablations confirm that all three reward components (consistency, resolution, synthesis quality) are necessary: removing any causes >2 F1 drop

> [!warning] Known limitations
> - AFB is constructed from synthetic data and two specific datasets (FinQA, Spider); generalizability to complex real-world scenarios requiring subjective fault attribution is unverified
> - Evaluation scope limited to structured QA and text-to-SQL — unclear how AOA manifests in open-domain agentic tasks

> [!question] Open questions
> - Does AOA scale with number of agents / reflection rounds, or does it saturate?
> - Can perspective-invariant training transfer to zero-shot multi-agent settings not seen during GRPO alignment?
> - Is the Thesis-Antithesis-Synthesis structure a general debiasing scaffold, or is it specific to fault attribution?

> [!tip] My take
> The AOA framing is genuinely useful — importing a well-studied cognitive psychology construct into the LLM agent reliability space is principled and opens clear measurement methodology. The dialectical CoT approach is clever as a *training signal design* (not just prompting). The 4B model outperforming larger ones is a credible parameter-efficiency claim given the specialization of the training objective.
>
> Main concern: the benchmark is entirely synthetic and the task domains (FinQA, Spider) have near-deterministic ground truth — this underspecifies how the method performs in genuinely ambiguous real-world attribution scenarios where there may be no correct answer. The "perspective-invariant" objective could be collapsing to a trivial solution (always pick the mode attribution) in structured tasks.

## Related

- [[actor-observer-asymmetry]]
- [[chain-of-thought-prompting]]
- [[fine-tuning]]
- supports: [[actor-observer-asymmetry-llm-agents]]
