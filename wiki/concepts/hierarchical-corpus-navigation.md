---
title: "Hierarchical Corpus Navigation"
aliases: ["corpus2skill", "navigable skill directory", "informational agent skills", "skill-based knowledge navigation", "corpus navigation", "agent corpus navigation", "offline corpus distillation", "navigable knowledge hierarchy"]
tags: [rag, agent-navigation, knowledge-distillation, hierarchical-clustering, agentic-rag, enterprise-qa, corpus-structure]
maturity: emerging
key_papers: [don-retrieve-navigate-distilling-enterprise-knowledge]
first_introduced: "2026"
date_updated: 2026-04-27
related_concepts: [skill-memory]
---

## Definition

**Hierarchical corpus navigation** is an alternative to passive retrieval (RAG) for grounding LLM responses in a document corpus. Rather than querying an index at serve time, the corpus is **compiled offline** into a hierarchical structure of summaries — a *skill directory* or *knowledge tree* — that an agent actively navigates at query time. The agent starts from a root overview, selects sub-branches based on query relevance, drills to progressively finer summaries, and ultimately retrieves full documents by ID.

The navigation hierarchy is typically built by:
1. Iteratively clustering documents until each cluster reaches a target granularity
2. Generating LLM summaries at each cluster level capturing topic areas, question types, and sub-cluster structure
3. Materializing the result as a tree of structured files (`SKILL.md` in Corpus2Skill)

The core contrast with standard RAG: the agent's view of the corpus is **topological** (I can see the shape of the knowledge space) rather than **proximity-based** (these are the closest documents to my query).

> [!tip] Intuition
> Standard RAG is like using a search engine: you submit a query and get ranked results without knowing the full shape of the library. Hierarchical corpus navigation is like using a card catalog or a well-organized filesystem: you start from a table of contents, drill into the relevant section, and find exactly what you need — and if you're in the wrong section, you can backtrack and try another.
>
> The key affordance is **backtracking** — you can see you're in the wrong branch and recover. Dense retrieval has no equivalent: a bad query gets bad results and the agent has no visibility into whether better results exist elsewhere.
>
> *Source: LLM analysis*

## Formal notation

Let $\mathcal{T} = (V, E)$ be the skill tree, with root $r$ and leaves $L \subseteq V$ corresponding to individual documents. Each node $v \in V$ holds a summary $s_v$ generated offline. The agent's navigation policy:

$$\pi(v' \mid v, q) = p_{\text{LLM}}\bigl(v' \mid s_v, q, \text{children}(v)\bigr)$$

selects the next node $v'$ to visit given the current node summary $s_v$, query $q$, and the set of child summaries. Navigation terminates when the agent selects a leaf (full document retrieval) or explicitly backtracks. The compilation function:

$$\mathcal{T} = \text{Compile}(\mathcal{D}, f_\text{cluster}, f_\text{summarize})$$

where $\mathcal{D}$ is the corpus, $f_\text{cluster}$ is the clustering function (e.g. k-means), and $f_\text{summarize}$ is the LLM summarization prompt.

## Variants

- **Corpus2Skill** ([[don-retrieve-navigate-distilling-enterprise-knowledge]]): materialized as a filesystem of `SKILL.md` files; agent navigates by reading file contents and selecting sub-directories. Evaluated on WixQA enterprise QA benchmark.
- **RAPTOR** (baseline comparison): tree-structured summaries built via hierarchical clustering; uses retrieval (not navigation) at serve time — the key distinction from Corpus2Skill. RAPTOR still submits a query to a search index; Corpus2Skill replaces search with agent-driven traversal.

## Comparison

| Paradigm | Corpus view | Backtracking | Serve-time infra | Failure mode |
|---|---|---|---|---|
| Dense retrieval (see [[retrieval-augmented-generation]]) | None (black-box index) | No | Vector DB required | Bad query → no recovery |
| RAPTOR | Tree summaries (retrieved) | No | Vector DB required | Same as dense + summary noise |
| Agentic RAG | None | Partial (retry queries) | Vector DB + tool wrappers | Retry loops, no structure |
| **Hierarchical corpus navigation** | Explicit tree topology | Yes | None (files only) | Root misrouting |

## When to use

- **High-value enterprise QA** where compile cost can be amortized over many queries and answer quality is critical
- **Multi-hop questions** where the answer requires combining evidence from structurally distant corpus branches
- **Low-latency serving without retrieval infrastructure** — the skill tree requires no vector database at serve time
- **Corpora with meaningful topical structure** — flat, heterogeneous corpora may not yield useful cluster hierarchies

> [!warning] Known limitations
> - **Compile staleness**: the hierarchy is static; corpus updates require full or partial recompilation
> - **Hard clustering brittleness**: k-means boundaries are hard; cross-cutting topics are artificially split
> - **Root routing fragility**: if the root summary does not clearly delineate topic regions, the agent misroutes at step 1 — the largest observed failure mode (38/62 failures in WixQA evaluation)
> - **Single-path navigation**: current formulations explore one branch at a time; parallel traversal is unexplored
> - **Compile LLM dependency**: summary quality is tightly coupled to the LLM used at compilation time; weaker compile-time models may produce ambiguous or overlapping cluster descriptions

> [!question] Open problems
> - Can hierarchical corpus navigation be made **incrementally updatable** without full recompilation?
> - What is the optimal cluster granularity as a function of corpus size and query distribution?
> - Does a **beam search** over branches (multi-path navigation) materially improve recall at acceptable latency cost?
> - How does the approach behave on corpora without strong topical structure (e.g. diverse Q&A archives, heterogeneous enterprise knowledge bases)?
> - Can the hierarchy be built without an LLM at compile time (e.g. extractive summarization + topic modelling)?

## Key papers

- [[don-retrieve-navigate-distilling-enterprise-knowledge]] (2026) — introduces Corpus2Skill; first empirical demonstration of navigation-over-retrieval on enterprise QA (WixQA); outperforms dense, hybrid, RAPTOR, and agentic RAG baselines
