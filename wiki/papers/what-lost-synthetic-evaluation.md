---
title: "What Has Been Lost with Synthetic Evaluation?"
slug: what-lost-synthetic-evaluation
arxiv: "2505.22830"
venue: "arXiv (cs.CL/cs.AI)"
year: 2025
tags: [synthetic-data, benchmark-validity, evaluation, difficulty, llm-generated-data, reading-comprehension]
importance: 3
date_added: 2026-04-21
source_type: pdf
s2_id: ""
keywords: [reasoning-over-text, benchmark validity, compositional reasoning, contrastive editing, LLM-generated data]
domain: NLP
code_url: "https://github.com/utahnlp/eval-synth-eval"
cited_by: []
---

## Problem

LLMs are increasingly used to generate evaluation benchmarks cheaply at scale. This raises a critical question: do LLM-generated benchmarks adequately substitute for human-authored ones? Specifically, **do they preserve the difficulty and reasoning challenge** that makes benchmarks useful for discriminating between model capabilities, or do they produce valid-looking but trivially easy data?

## Key idea

Systematic comparison of LLM-generated vs. human-authored versions of two high-quality reading comprehension benchmarks:

- **CondaQA**: reasoning about negation (requires understanding scope and polarity of negative statements)
- **DROP**: reasoning about quantities (requires numerical operations over text)

The paper evaluates two axes independently:

1. **Validity** — does the generated data follow the annotation guidelines? (measured by human preference studies)
2. **Difficulty** — does the generated data challenge models as much as the original? (measured by model accuracy and ranking preservation)

Key finding: **LLMs produce valid data cheaply, but validity and difficulty decouple**. Generated data is guideline-compliant but consistently easier for models, and fails to preserve model performance rankings.

## Method

### Generation process

1. Prompt an LLM (GPT-4 class) to generate new examples for each benchmark using iterative prompt engineering (refined prompts + filtering strategies).
2. Filter outputs for guideline compliance using an automated validity check.
3. Two output artifacts per benchmark: LLM-generated variants vs. original human-authored datasets.

### Evaluation

**Validity (human preference study)**:
- NLP researchers compare human-authored vs. LLM-generated questions/edits.
- Finding: human-authored items are preferred as more valid and better-aligned with dataset specifications.

**Difficulty (model evaluation)**:
- Multiple LLMs evaluated on both human-authored and LLM-generated versions.
- Metrics: accuracy (individual items) + **bundle consistency** (whether models consistently answer a contrastive pair correctly — key for detecting shortcut exploitation).
- Findings:
  - Models score higher on LLM-generated data than human-authored data across all configurations.
  - LLM-generated data **does not preserve model ordering** — rankings differ from human-authored benchmarks.
  - Bundle consistency collapses: 36.2% on human-authored (CondaQA) vs. 81.6% on LLM-generated — generated items lack the contrastive challenge that penalizes shortcut exploitation.

## Results

| Metric | Human-authored | LLM-generated |
|---|---|---|
| Validity (researcher preference) | Preferred | Less preferred |
| Model accuracy | Lower (harder) | Higher (easier) |
| Bundle consistency (CondaQA) | 36.2% | 81.6% |
| Model performance ranking preservation | Yes | No |

Cost-efficiency gain: LLM generation is a fraction of crowdsourcing cost, but yields qualitatively different (easier) evaluation data.

> [!warning] Limitations
> - **Two tasks only**: findings on negation and quantity reasoning may not generalize to all reasoning types. Tasks requiring commonsense or world knowledge might show different patterns.
> - **Prompt engineering may trade difficulty for validity**: the refinement process (filtering for validity) may systematically select for easier cases.
> - **No diversity/coverage analysis**: the paper evaluates validity and difficulty but not whether generated data covers the same linguistic phenomena as human-authored data.
> - **Single LLM generator (GPT-4 class)**: results may differ with weaker generators or fine-tuned data generators.

> [!question] Open questions
> - Is the difficulty gap fundamental to LLM generation, or is it a consequence of the filtering/validity optimization? Can you design generation processes that sacrifice some validity to preserve difficulty?
> - Does the difficulty gap hold for **non-reasoning tasks** (factuality, coherence)? The pattern may be specific to tasks requiring multi-step logical inference.
> - Can **adversarial prompting** or explicit difficulty targets in the generation prompt close the gap?
> - How does the gap compound in **iterative calibration loops** (IPC, GEI) — do prompt optimizers or judge refinement loops trained on easy synthetic data fail to transfer to hard real data?

> [!tip] My take
> The bundle consistency collapse (36.2% → 81.6%) is the sharpest finding in the paper. Bundle consistency is the metric that penalizes shallow heuristics — if a model can't consistently answer a (question, negated-question) pair correctly, it's relying on shortcuts. LLM-generated data all but eliminates this signal, meaning calibration loops trained on synthetic data won't learn to penalize shortcut-exploiting models.
>
> Direct implication for Optimisation Phase 2.1: the synthetic $z$ conditions (a1, a3) are both verbal descriptions of a rule deficit. If the Decomposed Golden Evaluator is calibrated primarily on synthetic signals, this paper is a direct warning that the evaluator may be systematically underestimating difficulty on real UW cases. The (a1)→(a3) gap the note intends to measure may be a floor effect if both conditions are too easy relative to production.
>
> The paper also partially explains why synthetic evaluation data is increasingly popular despite this gap: it's cheap and valid by most surface measures. The problem only becomes visible when you specifically measure difficulty / ranking-preservation, which most practitioners skip.
>
> *Source: LLM analysis*

## Related

- [[synthetic-evaluation-quality-gap]] — concept this paper anchors
- supports: [[llm-generated-evaluation-benchmarks-sacrifice-difficulty]]
- [[synthetic-boundary-case-optimization]] — adjacent; this paper warns against over-reliance on the type of synthetic data that IPC and GEI generate
- [[generate-evaluate-iterate-synthetic-data-human]] — complementary finding; GEI shows synthetic data is as effective as human data for criteria refinement, while this paper shows it is less difficult
- [[intent-based-prompt-calibration-enhancing-prompt]] — IPC uses synthetic boundary cases; this paper provides a caution about synthetic data quality
- Authors: Alexander Gill, Abhilasha Ravichander, Ana Marasović
