---
title: "Granularity"
slug: granularity
aliases: ["fine-grained", "coarse-grained", "granular"]
domain: ""
date_updated: 2026-04-13
---

## Definition

**Granularity** refers to the level of detail at which a system operates, evaluates, or provides feedback. **High granularity** (fine-grained) breaks a process into many small, individually inspectable units; **low granularity** (coarse-grained) operates on larger aggregate units.

## Context

In LLM evaluation, granularity determines how a judge reports failures. A low-granularity judge returns a single overall score or verdict ("the agent failed"). A high-granularity judge breaks the evaluation into specific steps, pinpoints where things went wrong, identifies which sub-goals succeeded vs. failed, and provides step-by-step feedback. The "agent-as-a-judge" literature argues that granularity is essential for useful evaluation of multi-step agentic outputs, because aggregate scores do not tell developers what to fix. The self-learning project's Classifier uses high granularity by design — its three steps (is feedback addressed? do instructions cover this? is data present?) each return a separate boolean + reason rather than a single aggregate classification.
