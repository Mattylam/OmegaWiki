---
title: "LLM-generated evaluation benchmarks sacrifice difficulty for validity compared to human-authored counterparts"
slug: llm-generated-evaluation-benchmarks-sacrifice-difficulty
status: weakly_supported
confidence: 0.65
tags: [synthetic-data, benchmark-validity, evaluation, difficulty, llm-generated-data]
domain: NLP
source_papers:
  - what-lost-synthetic-evaluation
evidence:
  - source: what-lost-synthetic-evaluation
    type: supports
    strength: moderate
    detail: "On CondaQA and DROP: models score higher on LLM-generated data than human-authored; bundle consistency collapses from 36.2% to 81.6% on CondaQA; model performance rankings not preserved across synthetic vs. human-authored versions."
conditions: |
  Demonstrated on reasoning-over-text tasks requiring multi-step inference (negation, quantity). May not hold for simpler factual QA, open-ended generation, or tasks where LLMs have strong domain knowledge. The gap may be reduced by explicit difficulty targeting in the generation prompt. Scope is benchmark creation; not directly tested on iterative calibration loops.
date_proposed: 2026-04-21
date_updated: 2026-04-21
---

> [!abstract] Statement
> LLM-generated evaluation data tends to be **guideline-valid but systematically easier** than human-authored data: models score higher on synthetic benchmarks, model performance rankings are not preserved, and contrastive bundle consistency (which penalizes shortcut exploitation) collapses. This represents a **validity–difficulty decoupling** that undermines the usefulness of synthetic benchmarks for discriminating model capabilities.

## Evidence summary

**Single-source, moderate support** — rests on [[what-lost-synthetic-evaluation]] (arXiv, May 2025):

- Two benchmarks studied: CondaQA (negation reasoning), DROP (quantity reasoning).
- Validity: human preference study shows human-authored items preferred, but LLM-generated items pass annotation guidelines at comparable rates.
- Difficulty: all tested models score higher on LLM-generated versions across both benchmarks.
- Bundle consistency (contrastive pair correctness rate): 36.2% human-authored → 81.6% LLM-generated on CondaQA. This is the sharpest evidence of shortcut-exploitability in synthetic data.
- Model ranking preservation: rankings differ between human-authored and LLM-generated benchmarks.

The claim is **weakly supported**: one paper, two tasks, single LLM generator (GPT-4 class).

> [!info] Conditions and scope
> Specifically about **contrastive/edit-based evaluation benchmarks for compositional reasoning**. The bundle consistency collapse is the most diagnostic metric — it directly measures whether items penalize shortcut exploitation. For non-contrastive benchmarks (single-question factual retrieval), this specific failure mode may not apply.
>
> Relationship to synthetic calibration loops: this claim provides a direct warning for systems like IPC ([[synthetic-boundary-case-generation-during-prompt]]) and GEI ([[synthetic-borderline-case-generation-effective-hand]]) that rely on synthetic signals — calibration on synthetic data may underestimate difficulty on real production data.

> [!warning] Counter-evidence
> - GEI ([[synthetic-borderline-case-generation-effective-hand]]) shows synthetic data is as effective as hand-crafted for judge criteria refinement — apparently contradictory. The reconciliation: GEI measures criteria-refinement effectiveness (does the judge improve?), not benchmark difficulty; the two studies measure different quantities.
> - The difficulty gap may be a consequence of validity-filtering rather than generation per se.

## Linked ideas

(none yet)

> [!question] Open questions
> - Can explicit difficulty targeting (adversarial generation prompts) close the bundle-consistency gap?
> - Does this finding extend to **synthetic feedback in prompt optimization loops** (e.g., does IPC-style calibration on synthetic boundaries produce prompts that generalize to harder real examples)?
> - Is the gap reducible by **using human-authored hard examples as seeds** for in-context generation?
