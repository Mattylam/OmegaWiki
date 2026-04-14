---
title: "Retrieval-Augmented Generation"
slug: "retrieval-augmented-generation"
domain: "NLP"
status: mainstream
aliases: ["RAG", "retrieval-augmented LLM", "grounded generation"]
first_introduced: "2020"
date_updated: 2026-04-13
source_url: "https://en.wikipedia.org/wiki/Retrieval-augmented_generation"
---

## Definition

Retrieval-augmented generation (RAG) is a technique that enables large language models to retrieve and incorporate new information from external data sources before generating a response. With RAG, LLMs first refer to a specified set of documents, then respond to user queries. These documents supplement information from the LLM's pre-existing training data, allowing the model to use domain-specific and/or updated information that is not available in the training data.

> [!tip] Intuition
> LLMs have a knowledge cutoff and cannot access private or real-time data. RAG bridges this gap by giving the model a retrieval step: before generating, the system searches a document store, retrieves relevant passages, and injects them into the model's context. The model then generates an answer grounded in the retrieved evidence rather than relying solely on memorized training data.
> *Source: LLM analysis*

## Formal notation

Given a query $q$, a retriever $R$ selects documents $D_q = R(q, \mathcal{C})$ from a corpus $\mathcal{C}$. The generator then conditions on both the query and retrieved documents:

$$y = \text{LM}(q, D_q)$$

The retriever is typically a dense embedding model (e.g., bi-encoder) or hybrid sparse+dense search.

## Key variants

- **Naive RAG**: single retrieval step followed by generation
- **Advanced RAG**: query rewriting, re-ranking, or iterative retrieval before generation
- **Modular RAG**: composable retrieval components (chunking, embedding, re-ranking) as independent modules
- **Agentic RAG**: the LLM decides when and what to retrieve via tool use
- **Multi-hop RAG**: multiple retrieval rounds for complex questions requiring information synthesis

> [!warning] Known limitations
> - Retrieval quality is the ceiling — if relevant documents are not retrieved, the model cannot use them
> - Long retrieved contexts can dilute the model's attention ("lost in the middle" problem)
> - No guarantee the model will faithfully use retrieved documents vs. its own training knowledge
> - Chunking strategy significantly affects retrieval quality and is domain-dependent
> *Source: LLM analysis*

> [!question] Open problems
> - Diagnosing whether failures are retrieval errors or generation errors
> - Optimal chunking and embedding strategies for domain-specific corpora
> - Handling contradictions between retrieved documents and model knowledge
> *Source: LLM analysis*

> [!info] Relevance to active research
> The AIUA research agents operate as RAG systems: they retrieve documents (internet search results, OSHA docs, loss runs, exposure docs) into `task_dict`, then generate analysis grounded in that context. The Classifier's Step 3 ("Is the required data present in the context?") directly evaluates RAG quality — determining whether a retrieval gap, not a prompt gap, is the root cause of a bad output. When Step 3 returns `data_present=False`, the classification is `not_addressable` — no prompt change can fix a retrieval failure.
> *Source: LLM analysis*
