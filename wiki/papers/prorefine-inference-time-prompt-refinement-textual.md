---
title: "ProRefine: Inference-Time Prompt Refinement with Textual Feedback"
slug: prorefine-inference-time-prompt-refinement-textual
arxiv: "2506.05305"
venue: "arXiv (cs.CL/cs.AI/cs.LG)"
year: 2025
tags: [prompt-optimization, inference-time, textual-feedback, reasoning, agentic-loop, dynamic-prompt-refinement]
importance: 3
date_added: 2026-04-21
source_type: pdf
s2_id: ""
keywords: [inference-time prompt refinement, agentic feedback loops, textual feedback optimization, LLM policy gradient, dynamic reasoning correction]
domain: NLP
code_url: ""
cited_by: []
---

## Problem

Agentic workflows depend critically on prompt quality, but poorly designed prompts can cause failures that cascade through multi-agent pipelines. Existing prompt optimization is **offline** (MIPROv2, GEPA, TextGrad) — it requires labeled training data or a fixed optimization budget before deployment. At inference time, there is no mechanism to detect and correct a prompt that is underperforming on a specific input without restarting the whole optimization loop.

## Key idea

**ProRefine** closes the loop at inference time: when the system detects a likely error in the current response (via a verifier), it triggers a textual feedback cycle — a dedicated feedback model diagnoses the failure, and a prompt optimizer LLM rewrites the prompt on-the-spot before retrying. No training data, no gradient access, and no ground truth labels are required.

Three components form the agentic loop:
1. **Task LLM**: executes the current prompt on the input.
2. **Feedback model**: reads the execution output and generates natural-language critique diagnosing the failure.
3. **Prompt optimizer**: reads the current prompt + feedback and proposes a revised prompt.

The **verifier** gates refinement: only outputs flagged as likely incorrect trigger the refinement loop, saving computation on correct responses.

## Method

### ProRefine loop

For each inference instance:

1. Run **Task LLM** with current prompt $p_t$ on input $x$; produce output $\hat{y}_t$.
2. **Verifier** checks $\hat{y}_t$: if likely correct → return $\hat{y}_t$; if likely incorrect → continue.
3. **Feedback model** receives $(p_t, x, \hat{y}_t)$ → produces textual critique $f_t$ diagnosing the failure.
4. **Prompt optimizer** receives $(p_t, f_t)$ → produces refined prompt $p_{t+1}$.
5. Repeat from step 1 with $p_{t+1}$ until verifier approves or max iterations reached.

### Verifier types

- **Oracle verifier**: uses ground truth (upper bound; shows peak performance achievable)
- **Optimal verifier**: a calibrated LLM classifier that predicts correctness without ground truth (used in deployment)
- **Baseline**: no verifier (always refine)

The verifier quality is the key practical bottleneck: a poor verifier triggers unnecessary refinements (cost) or misses real failures (quality loss).

### Baselines

- **Zero-shot CoT**: plain chain-of-thought prompting without refinement
- **Self-Refine**: iterative self-feedback without a separate feedback model or prompt optimizer
- **Reflexion**: reflection-based refinement with memory

## Results

Evaluated on **5 mathematical reasoning datasets**: GSM8K, MATH, AQuA, SVAMP, SingleEq.

| Configuration | Gain over zero-shot CoT |
|---|---|
| ProRefine (optimal verifier) | +3 to +37 pp |
| Best result (complex tasks) | +37 pp on a challenging MATH subset |
| ProRefine (smaller model) | approaches larger model baseline |

- Refinement iterations are low on average (1-2 per instance), making inference cost manageable.
- Larger base models benefit more from ProRefine (more capacity to execute feedback).
- On simple tasks (SingleEq), gains are marginal — error rate is already low.

> [!warning] Limitations
> - **Verifier quality bottleneck**: performance depends critically on the verifier accurately detecting failures. A miscalibrated verifier causes unnecessary cost (false positives) or silent failures (false negatives).
> - **No convergence guarantee**: the refinement loop may not converge; the paper relies on iteration budget as the backstop.
> - **Math-only evaluation**: results are on mathematical reasoning; generalization to open-ended or knowledge-intensive tasks is untested.
> - **Computational cost**: 3 LLM calls per refinement iteration (task + feedback + optimizer), plus the verifier. For tasks requiring multiple refinements, this multiplies cost.
> - **Feedback model and optimizer are separate from task LLM**: using 3 distinct models assumes a black-box deployment architecture; self-refinement with a single model may perform differently.

> [!question] Open questions
> - Can the **verifier be learned** from ProRefine's own refinement history (adaptive verifier)?
> - Does ProRefine compose with **offline prompt optimization** (e.g., GEPA-refined prompt as the starting prompt for ProRefine's inference-time loop)?
> - What is the **right granularity of feedback** — whole-prompt revision vs. step-level correction? Step-level correction would reduce the risk of prompt drift.
> - Does the inference-time refinement **generalize across inputs** — do prompts refined on one instance help on subsequent similar instances, or is each refinement ephemeral?

> [!tip] My take
> ProRefine is mechanistically an inference-time version of reflective prompt evolution (GEPA) — the same textual feedback → prompt revision loop, but triggered per-instance during deployment rather than run offline. The clean separation of feedback model / optimizer / verifier makes the architecture modular and the failure modes explicit.
>
> The 37 pp gain on complex math tasks is impressive, but the verifier dependency is the real constraint. In production, a ground-truth verifier isn't available — you need an LLM that can reliably detect errors without labels. That's the same problem as LLM-as-judge evaluation quality, and the same biases apply.
>
> For the Optimisation Phase 2.1 loop: ProRefine shows that the Refiner→Evaluator→Refiner loop can be run at inference time rather than as an offline batch process. The 2.1 loop is essentially ProRefine's training-time analogue — both use textual feedback to drive prompt revision; 2.1 does it against a held-out test split to validate generalization, which ProRefine doesn't address.
>
> *Source: LLM analysis*

## Related

- [[prompt-optimization]] — foundation this paper extends
- [[reflective-prompt-evolution]] — extends with inference-time variant; ProRefine is an online/inference-time instance of the same textual feedback → prompt revision mechanism
- supports: [[inference-time-agentic-prompt-refinement-textual]]
- [[gepa-reflective-prompt-evolution-outperform-reinforcement]] — offline analogue; GEPA optimizes during training, ProRefine at inference
- [[synthetic-boundary-case-optimization]] — complementary; IPC synthesizes boundary cases offline, ProRefine detects and corrects failures online
- Authors: Deepak Pandita, Tharindu Cyril Weerasooriya, Ankit Parag Shah, Isabelle Diana May-Xin Ng, Christopher M. Homan, Wei Wei
