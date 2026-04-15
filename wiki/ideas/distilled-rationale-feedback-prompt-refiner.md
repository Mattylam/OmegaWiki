---
title: "Distilled rationale feedback for Prompt Refiner"
slug: distilled-rationale-feedback-prompt-refiner
status: proposed
origin: "Optimisation Phase note — preserve full rationale r_i for accountability while blocking y* leakage to PR via a two-stage evaluator"
origin_gaps: [bootstrapping-produces-better-demonstrations-than-hand-written]
tags: [prompt-optimization, golden-evaluator, distillation, information-hiding, self-learning-phase-2]
domain: NLP
priority: 1
pilot_result: ""
failure_reason: ""
linked_experiments: []
date_proposed: 2026-04-13
date_resolved: ""
---

## Motivation (deprioritised under reframing)

This idea was originally motivated by preserving r_i for **accountability** in a production context — a concern that is reduced under the reframed purpose of GE ([[golden-evaluator-calibration-baseline]]) as an offline testing instrument. When GE is a benchmark component, accountability matters less (you can always re-run the benchmark and inspect r_i directly in the test-harness logs); what matters is a *clean measurable signal*.

The 2-stage structure (GE → r_i → Distiller → f_i) adds two liabilities to a benchmarking context:
1. **Distiller variance becomes a confounder**: when measuring PR performance, you can no longer distinguish "PR improved" from "Distiller produced better f_i this run" — the benchmark measures PR + Distiller jointly
2. **More moving parts in the test harness**: the Distiller is another prompt to maintain, another calibration surface, another place the benchmark can silently break

For these reasons, this idea is deprioritised relative to the taxonomic approach ([[taxonomic-feedback-prompt-refiner]]), which produces a similarly y*-free signal with fewer confounders. It remains documented here because it may be the right architecture for a *deployed* Optimisation Phase where accountability is again a real requirement — but it is not the right first move for the testing-ground phase.

**Original motivation preserved below** for reference:

The original Optimisation Phase note explicitly retains r_i for "accountability and consistency" and excludes it from PR specifically because r_i contains y* references. A natural question: can we keep r_i (for audit) AND derive a y*-free f_i from it (for PR), preserving the full rationale information without the leak? This is a distillation / projection problem: run r_i through a second LLM that has no access to y* and whose prompt explicitly strips y* content.

## Hypothesis

If r_i is post-processed by a **Feedback Distiller** — a separate LLM with no access to y*, prompted to extract PR-actionable feedback while removing all y* references — then:
1. r_i is preserved verbatim for accountability
2. f_i carries richer signal than Option A's closed vocabulary (free-form distilled insights) while being testably y*-free
3. The distillation step is auditable: a reconstruction attack can be mounted against f_i to verify no leak

## Approach sketch

**Two-stage evaluator**:

```
(ŷ_i, y*) ─→ GE ─→ r_i (private — stored for accountability)
                           │
                           ▼
(r_i, task_context) ─→ Distiller (no y* access) ─→ f_i (public — given to PR)
```

The Distiller's prompt explicitly instructs it to:
- Extract only reasoning-pattern observations (what kind of error, where in the output, why it matters)
- Avoid quoting or paraphrasing y* content
- Produce a fixed schema (criterion, action verb, target scope) to constrain output structure

**Leakage audit** (run quarterly or after Distiller prompt changes):
1. Collect N distilled f_i's across accounts
2. Train / prompt a reconstruction LLM to predict y* from f_i
3. Measure reconstruction similarity to actual y* vs random baseline
4. Fail the Distiller if reconstruction beats baseline

**Accountability preserved**: when a prompt change is approved or rejected, r_i is the audit trail; f_i is the training signal.

## Expected outcome

- Richer signal to PR than Option A (free-form insights beat closed tags when tags don't fit)
- Accountability requirement fully met (r_i retained)
- Convergence speed somewhere between Option A (fast, structured) and Option B (slow, preference-only)
- Adds one LLM call per iteration (compile-time cost), but enables the PR to leverage reasoning-level observations rather than only pattern-matching tags

> [!warning] Risks
> - **Distiller leaks**: the Distiller is a prompt; prompts fail in subtle ways. A single y*-containing phrase in r_i that the Distiller forgets to strip defeats the whole scheme. Mitigation: automated reconstruction-attack gate in CI; run on every Distiller prompt change.
> - **Added failure surface**: one more LLM call, one more prompt to maintain, one more point where things break. If Distiller misinterprets r_i, PR gets wrong signal → silent degradation.
> - **Cost + latency**: +1 LLM call per iteration; at N=10 iterations per session × many sessions, this adds up.
> - **[[adversarial-response]] and [[evaluator-collusion]] risk compound**: now you have three LLMs in the loop (GE, Distiller, PR) that may share base models. Collusion surface triples.
> - **Distilled output may still reveal y***: paraphrased concrete content can leak structural information about y* even without quoting. The reconstruction audit is the only honest test.
> - **Not information-theoretically clean** like Option B — the question is always "does the audit pass?" rather than "can it possibly leak?"
> - **Benchmark confounding** (new under reframing): PR performance measurements are contaminated by Distiller behaviour. Any observed PR gain could be attributable to Distiller improvement rather than PR improvement. This is a major liability in a testing-ground context and is the primary reason for deprioritisation.

> [!success] Pilot results
> *(not yet run)*

> [!info] Lessons learned
> *(not yet available)*
