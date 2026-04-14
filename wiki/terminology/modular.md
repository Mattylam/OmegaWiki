---
title: "Modular"
slug: modular
aliases: ["modularity", "modular design", "composable"]
domain: ""
date_updated: 2026-04-13
---

## Definition

A system is **modular** when it is built from general-purpose, interchangeable components that abstract specific tasks away from their low-level implementation. A modular component can be composed with others, replaced by an equivalent alternative without redesigning the whole pipeline, and optimised independently of the rest of the system.

## Context

In LLM systems, modularity is the opposite of hard-coded prompt templates. Modular frameworks like DSPy treat pipeline stages as signatures (declarative `what`) and modules (implementations of `how`), so a `ChainOfThought` module can be dropped in wherever a `Predict` module was used. Teleprompters (optimisers) operate on this structure independently of any specific pipeline. In the self-learning project, the V1 pipeline is deliberately modular — Classifier, Prompt Refiner, Output Evaluator, Regression Check — so that each module is independently optimisable in Phase 2.
