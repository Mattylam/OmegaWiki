---
title: "Fine-Tuning"
slug: "fine-tuning"
domain: "NLP"
status: mainstream
aliases: ["model fine-tuning", "transfer learning (fine-tuning)", "supervised fine-tuning", "SFT"]
first_introduced: "2018"
date_updated: 2026-04-13
source_url: "https://en.wikipedia.org/wiki/Fine-tuning_(deep_learning)"
---

## Definition

Fine-tuning is the process of adapting a pre-trained model to perform a different, usually more specific, task by continuing training on a smaller, task-specific dataset. It updates the model's weights using gradient descent on the new data, transferring knowledge learned during pre-training to the target task. It is a form of transfer learning.

> [!tip] Intuition
> A pre-trained LLM has general language understanding but may not perform well on a specific domain or task format. Fine-tuning takes this general-purpose model and specializes it by training on examples of the desired behavior. Unlike prompt engineering, which shapes behavior through input text alone, fine-tuning permanently modifies the model's parameters.
> *Source: LLM analysis*

## Formal notation

Given a pre-trained model with parameters $\theta_0$ and a task-specific dataset $D_{\text{task}} = \{(x_i, y_i)\}$:

$$\theta^* = \arg\min_\theta \sum_{(x,y) \in D_{\text{task}}} \mathcal{L}(f_\theta(x), y)$$

initialized from $\theta_0$, typically with a lower learning rate than pre-training.

## Key variants

- **Full fine-tuning**: update all model parameters
- **Parameter-efficient fine-tuning (PEFT)**: update only a small subset of parameters (LoRA, adapters, prefix tuning)
- **Instruction tuning**: fine-tuning on instruction-following datasets to improve prompt adherence
- **RLHF (Reinforcement Learning from Human Feedback)**: fine-tuning with a reward model trained on human preferences
- **DPO (Direct Preference Optimization)**: aligning model behavior without an explicit reward model
> [!warning] Known limitations
> - Requires labeled training data (typically hundreds to thousands of examples)
> - Risk of catastrophic forgetting — the model may lose general capabilities
> - Expensive: requires GPU compute for training
> - Model versioning and deployment complexity
> - Results are coupled to a specific model checkpoint
> *Source: LLM analysis*

> [!question] Open problems
> - Efficient fine-tuning with minimal data
> - Preventing catastrophic forgetting while specializing
> - Combining fine-tuning with prompt optimization for best results
> *Source: LLM analysis*

> [!info] Relevance to active research
> Fine-tuning is the key contrast point to DSPy's prompt optimization approach. DSPy explicitly does not fine-tune — it optimizes prompt text (instructions and demonstrations) while keeping model weights frozen. The self-learning project chose prompt optimization over fine-tuning for several reasons: lower data requirements (20-50 examples vs. hundreds), no GPU compute needed for optimization, and the ability to swap models without re-training. However, DSPy's `BootstrapFinetune` optimizer can use bootstrapped demonstrations to fine-tune a smaller model — a potential Phase 2 extension if prompt optimization plateaus.
> *Source: LLM analysis*
