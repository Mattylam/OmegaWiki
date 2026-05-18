---
title: "Generate, Evaluate, Iterate: Synthetic Data for Human-in-the-Loop Refinement of LLM Judges"
slug: generate-evaluate-iterate-synthetic-data-human
arxiv: "2511.04478"
venue: "arXiv (cs.HC/cs.AI)"
year: 2025
tags: [synthetic-data, llm-as-judge, human-in-the-loop, judge-refinement, borderline-case-generation, evaluation]
importance: 3
date_added: 2026-04-21
source_type: pdf
s2_id: ""
keywords: [synthetic data generation, LLM-as-a-judge, human-LLM alignment, borderline case generation, interactive evaluation]
domain: NLP
code_url: ""
cited_by: []
---

## Problem

LLM-as-a-judge systems require diverse, representative test cases for users to iteratively refine their evaluation criteria. In practice, collecting sufficient real-world data is slow and expensive, and the data skews toward easy cases that do not stress-test the criteria. The result is that users cannot efficiently calibrate judge criteria to edge cases — they run out of representative test data before they've found the hard cases.

## Key idea

Integrate a **synthetic data generation tool** (EvalAssist) directly into the LLM-as-a-judge criteria-refinement loop. Users configure generation parameters (domain, persona, response length, desired outcome type) and explicitly request **borderline cases** — instances near the decision boundary that most expose ambiguities in the current evaluation criteria. An LLM generates these cases on demand; users can AI-assist-edit existing cases inline. The generation prompts and rationale are surfaced transparently so users understand what the LLM was instructed to produce.

The key design claim: synthetic borderline cases are as effective as hand-crafted cases for criteria refinement, and users strongly prefer synthetic generation due to speed and diversity.

## Method

### EvalAssist loop

1. User defines evaluation criteria (rubric / natural language) for a task.
2. User opens the synthetic data panel and configures:
   - **Domain and persona**: e.g. formal complaint, casual social media, domain-specific expert.
   - **Length and style**: short / long / naturally messy.
   - **Outcome type**: clear positive, clear negative, or **borderline** (near the decision boundary under the current criteria).
3. EvalAssist calls a generation LLM with a structured meta-prompt (Appendix C) that produces $k$ synthetic test cases matching the configuration. Prompts and rationale are surfaced inline.
4. User evaluates the synthetic cases using the judge, observes failures, refines criteria, and regenerates.
5. AI-assisted inline editing: user can request the LLM to alter a specific case (change persona, flip outcome, increase ambiguity).

### User study design

- N=24 practitioners (diverse roles: ML engineers, UX researchers, data scientists).
- Two conditions (within-subject, counterbalanced): **Synthetic** (EvalAssist) vs. **Manual** (collecting / writing test cases by hand).
- Two tasks: evaluating for bias (sensitive domain), evaluating for politeness.
- Metrics: criteria refinement effectiveness (judge alignment), task load (NASA-TLX), user preference.

## Results

- **83% preferred synthetic** over manual (χ²(2) = 27, p < .05).
- **Criteria refinement effectiveness** (judge–human alignment delta): synthetic data statistically equivalent to hand-crafted data — no significant difference in improvement achieved.
- Key qualitative themes: synthetic generation enables coverage of cases users would not have thought to write; borderline case generation directly exposes criteria ambiguities.
- Synthetic data produced notably higher **volume and topic diversity** within the same time budget.
- Limitations surfaced by users: synthetic text lacks natural messiness / idiosyncratic errors of real-world data; lexical variability could be improved.

> [!warning] Limitations
> - **Realism gap**: synthetic cases are cleaner and more formal than real production data — may not stress-test criteria for handling noisy, code-switched, or adversarially-phrased inputs.
> - **Small study scope**: N=24, 2 tasks (bias, politeness); generalization across diverse judge applications is untested.
> - **Automated criteria refinement not studied**: the paper evaluates human-driven refinement; whether the loop can be closed without a human is an open question.
> - **No longitudinal evaluation**: user study is a single session; whether synthetic-calibrated criteria degrade over time as real data distribution shifts is unknown.

> [!question] Open questions
> - Can synthetic data generation be **fully automated** (no human in the loop for refinement) by combining EvalAssist-style borderline generation with IPC-style calibration?
> - Does the **realism gap** matter in practice — do users who calibrate on synthetic borderlines make worse judgements on real data over time?
> - How does borderline case generation quality depend on the **strength of the generation LLM**?
> - Can this compose with **trajectory-level reflection** (GEPA-style) — generating borderline cases from observed LLM failures rather than from user-configured parameters?

> [!tip] My take
> The headline finding — synthetic borderlines are as effective as hand-crafted cases for judge refinement — directly validates the use of synthetic signals in any LLM calibration loop, including the Optimisation Phase 2.1 note's approach. The user preference result (83%) is strong for a UX study.
>
> The deeper contribution is the design of the **borderline case** as a first-class generation target. Most synthetic data work generates "representative" examples; this paper specifically targets the decision boundary. This is the same intuition behind IPC's boundary case synthesis, applied to the judge rather than the prompt.
>
> Limitation worth tracking: the realism gap. EvalAssist's synthetic cases are explicitly described by users as more formal and cleaner than real data. For the Phase 2.1 experiment, the $z$ condition (a1) is verbatim and (a3) is abstract — neither is tested against "naturally messy" UW feedback, which may have different calibration properties.
>
> *Source: LLM analysis*

## Related

- [[llm-as-judge]] — foundation; this paper extends LLM-as-judge with a synthetic data loop
- [[synthetic-boundary-case-optimization]] — extends with a human-in-the-loop judge refinement variant
- supports: [[synthetic-borderline-case-generation-effective-hand]]
- [[intent-based-prompt-calibration-enhancing-prompt]] — sister paper; IPC generates boundary cases for prompt optimization (automated), this paper generates them for judge refinement (human-in-the-loop)
- Authors: Hyo Jin Do, Zahra Ashktorab, Jasmina Gajcin, Erik Miehling, Martín Santillán Cooper, Qian Pan, Elizabeth M. Daly, Werner Geyer
