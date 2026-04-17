# Wiki Log

> Append-only chronological log. Entry format: `## [YYYY-MM-DD] action | details`
## [2026-04-13] prefill | 14 foundations created for self-learning project domain (prompt-engineering, prompt-optimization, few-shot-prompting, chain-of-thought-prompting, in-context-learning, llm-as-judge, human-in-the-loop-learning, retrieval-augmented-generation, react-prompting, sycophancy, goodharts-law, mode-collapse, regression-testing, fine-tuning)
## [2026-04-14] ingest | claims for memento-skills-let-agents-design-agents: 0 matched existing, 1 new
## [2026-04-14] ingest | concepts for memento-skills-let-agents-design-agents: 0 matched existing, 1 new, 6 foundation-refs
## [2026-04-14] ingest | added papers/memento-skills-let-agents-design-agents | new: claims/continual-learning-via-external-memory, concepts/skill-memory, people/huichi-zhou, people/jun-wang, people/siyuan-guo | foundation-refs: human-in-the-loop-learning, fine-tuning, in-context-learning, retrieval-augmented-generation, react-prompting, prompt-optimization | edges: 8
## [2026-04-14] terminology | added page type + 3 pages: modular, pervasive, granularity (glossary for generic vocabulary, terminal like foundations)
## [2026-04-14] prefill | +7 foundations (adversarial-response, evaluator-collusion, scratchpad, kendalls-tau, spearmans-rho, pearson-correlation, ordinal-ranking) + 1 terminology (brittle), all sourced from Obsidian work vault
## [2026-04-14] ingest | claims for dspy-compiling-declarative-language-model-calls: 0 matched existing, 2 new (at limit for importance=5)
## [2026-04-14] ingest | concepts for dspy-compiling-declarative-language-model-calls: 0 matched existing, 3 new (at limit for importance=5), 8 foundation-refs
## [2026-04-14] ingest | added papers/dspy-compiling-declarative-language-model-calls | new: claims/dspy-compiled-programs-outperform-hand-crafted-prompts, claims/bootstrapping-produces-better-demonstrations-than-hand-written, concepts/dspy-signatures, concepts/teleprompters, concepts/bootstrap-dspy, people/omar-khattab, people/christopher-potts, people/matei-zaharia | foundation-refs: prompt-optimization, prompt-engineering, few-shot-prompting, chain-of-thought-prompting, in-context-learning, react-prompting, scratchpad, fine-tuning | edges: 13
## [2026-04-14] ideate | 3 design alternatives for Optimisation Phase feedback (y*-leakage prevention): ideas/taxonomic-feedback-prompt-refiner (priority=4), ideas/pairwise-preference-feedback-prompt-refiner (priority=3), ideas/distilled-rationale-feedback-prompt-refiner (priority=2) | all linked to claims/bootstrapping-produces-better-demonstrations-than-hand-written via addresses_gap
## [2026-04-14] ingest | claims for when-ais-judge-ais-rise-agent: 0 matched existing, 1 new
## [2026-04-14] ingest | concepts for when-ais-judge-ais-rise-agent: 0 matched existing, 0 new, 10 foundation-refs (survey paper: references existing concepts rather than introducing new ones)
## [2026-04-14] ingest | added papers/when-ais-judge-ais-rise-agent | new: claims/multi-agent-debate-outperforms-single-agent-judge, people/fangyi-yu | foundation-refs: llm-as-judge, sycophancy, adversarial-response, evaluator-collusion, mode-collapse, goodharts-law, kendalls-tau, spearmans-rho, ordinal-ranking, chain-of-thought-prompting | edges: 11
## [2026-04-14] ideate | +1 idea: golden-evaluator-calibration-baseline (priority=5, prerequisite to the 3 feedback-design ideas). Reframed: GE is a testing instrument for PR optimisation, not a production quality gate. Before feedback design is chosen, GE's reliability as oracle must be established via Kendall's τ against UW judgments.
## [2026-04-14] reframe | updated priorities for 3 feedback-design ideas under GE-as-testing-instrument framing: taxonomic (4→5), pairwise (3→2, re-scoped as outer-loop comparator not inner-loop signal), distilled (2→1, deprioritised due to benchmark-confounding)
## [2026-04-15] ingest | concepts for judging-judges-systematic-study-position-bias: 1 matched (foundation llm-as-judge), 1 new (position-bias-llm-judge), 1 foundation-refs
## [2026-04-15] ingest | claims for judging-judges-systematic-study-position-bias: 0 matched existing, 1 new
## [2026-04-15] ingest | added papers/judging-judges-systematic-study-position-bias | new: concepts/position-bias-llm-judge, claims/llm-judges-exhibit-measurable-position-bias
## [2026-04-15] ingest | concepts for self-preference-bias-llm-judge: 0 matched existing, 1 new, 1 foundation-refs
## [2026-04-15] ingest | claims for self-preference-bias-llm-judge: 0 matched existing, 1 new
## [2026-04-15] ingest | added papers/self-preference-bias-llm-judge | new: concepts/self-preference-bias, claims/llm-judges-over-reward-low-perplexity-outputs
## [2026-04-15] ingest | concepts for justice-prejudice-quantifying-biases-llm-judge: 2 matched existing, 1 new, 1 foundation-refs
## [2026-04-15] ingest | claims for justice-prejudice-quantifying-biases-llm-judge: 0 matched existing, 1 new
## [2026-04-15] ingest | added papers/justice-prejudice-quantifying-biases-llm-judge | new: concepts/llm-judge-bias-taxonomy, claims/llm-judges-exhibit-systematic-multi-category-biases
## [2026-04-15] ingest | concepts for judges-verdict-comprehensive-analysis-llm-judge: 0 matched existing, 1 new, 3 foundation-refs
## [2026-04-15] ingest | claims for judges-verdict-comprehensive-analysis-llm-judge: 0 matched existing, 1 new
## [2026-04-15] ingest | added papers/judges-verdict-comprehensive-analysis-llm-judge | new: concepts/human-agreement-benchmark-llm-judge, claims/correlation-is-insufficient-for-llm-judge-evaluation
## [2026-04-15] ingest | concepts for am-more-pointwise-pairwise-revealing-position: 1 matched existing (position-bias-llm-judge), 1 new (balanced-permutation-aggregation), 1 foundation-refs
## [2026-04-15] ingest | claims for am-more-pointwise-pairwise-revealing-position: 0 matched existing, 1 new
## [2026-04-15] ingest | added papers/am-more-pointwise-pairwise-revealing-position | new: concepts/balanced-permutation-aggregation, claims/rubric-based-pointwise-scoring-is-implicitly-multi-choice
## [2026-04-15] idea | added ideas/decomposed-reference-based-golden-evaluator | reverse links: 3 claims, 1 idea
## [2026-04-15] ingest | concepts for factscore-fine-grained-atomic-evaluation-factual: 0 matched existing, 1 new, 1 foundation-refs
## [2026-04-15] ingest | claims for factscore-fine-grained-atomic-evaluation-factual: 0 matched existing, 1 new
## [2026-04-15] ingest | added papers/factscore-fine-grained-atomic-evaluation-factual | new: concepts/atomic-fact-decomposition, claims/decomposing-generation-into-atomic-facts-enables-fine-grained-evaluation
## [2026-04-15] ingest | concepts for closer-look-claim-decomposition: 1 matched existing (atomic-fact-decomposition), 0 new, 1 foundation-refs
## [2026-04-15] ingest | claims for closer-look-claim-decomposition: 0 matched existing, 1 new
## [2026-04-15] ingest | added papers/closer-look-claim-decomposition | new: claims/factuality-metrics-are-sensitive-to-decomposition-method
## [2026-04-15] ingest | concepts for decmetrics-structured-claim-decomposition-scoring-factually: 1 matched existing (atomic-fact-decomposition), 1 new (decomposition-quality-metrics), 1 foundation-refs
## [2026-04-15] ingest | claims for decmetrics-structured-claim-decomposition-scoring-factually: 0 matched existing, 1 new
## [2026-04-15] ingest | added papers/decmetrics-structured-claim-decomposition-scoring-factually | new: concepts/decomposition-quality-metrics, claims/decomposer-quality-can-be-measured-automatically
## [2026-04-15] idea-update | decomposed-reference-based-golden-evaluator: added extractor-calibration prerequisite stage, three-way NLI correctness, VITAL-style importance weighting; wired 3 new origin_gaps
## [2026-04-15] idea-update | decomposed-reference-based-golden-evaluator: added Format assumption section, per-section pipeline, grounding-drift diagnostic, section-level aggregation, scoped in-bullet splitter calibration
## [2026-04-15] ingest | concepts for logic-lm-empowering-llms-symbolic-solvers: 0 matched, 2 new (neurosymbolic-llm-verification, autoformalization), 1 foundation-refs
## [2026-04-15] ingest | claims for logic-lm-empowering-llms-symbolic-solvers: 0 matched, 1 new
## [2026-04-15] ingest | added papers/logic-lm-empowering-llms-symbolic-solvers | new: concepts/neurosymbolic-llm-verification, concepts/autoformalization, claims/llm-solver-integration-improves-faithful-reasoning
## [2026-04-15] ingest | concepts for proof-thought-neurosymbolic-program-synthesis: 2 matched existing, 0 new, 1 foundation-refs
## [2026-04-15] ingest | claims for proof-thought-neurosymbolic-program-synthesis: 0 matched existing, 1 new
## [2026-04-15] ingest | added papers/proof-thought-neurosymbolic-program-synthesis | new: claims/dsl-mediated-autoformalization-improves-translation-robustness
## [2026-04-15] ingest | concepts for solver-aided-verification-policy-compliance-tool: 2 matched existing, 1 new (policy-compliance-smt-formalization), 1 foundation-refs
## [2026-04-15] ingest | claims for solver-aided-verification-policy-compliance-tool: 0 matched existing, 1 new
## [2026-04-15] ingest | added papers/solver-aided-verification-policy-compliance-tool | new: concepts/policy-compliance-smt-formalization, claims/policy-rules-can-be-formalized-as-smt-constraints
## [2026-04-15] ingest | concepts for neuro-symbolic-compliance-integrating-llms-smt: 3 matched existing, 0 new, 1 foundation-refs
## [2026-04-15] ingest | claims for neuro-symbolic-compliance-integrating-llms-smt: 0 matched existing, 1 new
## [2026-04-15] ingest | added papers/neuro-symbolic-compliance-integrating-llms-smt | new: claims/minimal-modification-smt-repair-enables-actionable-compliance-diagnostics
## [2026-04-15] idea | added ideas/per-case-smt-routed-correctness-branch | reverse links: 4 claims, 1 idea
## [2026-04-15] foundation | added foundations/natural-language-inference; updated ideas/per-case-smt-routed-correctness-branch with wikilinks
## [2026-04-15] idea-rewrite | decomposed-reference-based-golden-evaluator: simplified to single bidirectional NLI matrix (pre-decomposed golden + LLM-decomposed agent); three metrics derived from row-wise and column-wise reads
## [2026-04-15] idea-fix | decomposed-reference-based-golden-evaluator: fixed metric algebra (correctness = |correct|/(|correct|+|wrong|), not /|A|); renamed 'extra' to 'out-of-scope'; added scope-adherence as distinct metric; restored deterministic length-based conciseness
## [2026-04-15] idea-fix | decomposed-reference-based-golden-evaluator: switched from pairwise NLI matrix to multi-premise NLI (two passes per section); eliminates tie-break edge case, addresses granularity mismatch; compute drops from O(|A|x|G|) to O(|A|+|G|)
## [2026-04-15] idea-update | decomposed-reference-based-golden-evaluator: added Phase-2 future-phase section linking to per-case-smt-routed-correctness-branch as NLI->SMT upgrade path
## [2026-04-16] ingest | claims for bert-judge-robust-alternative-lexical-methods: 0 matched existing, 1 new
## [2026-04-16] ingest | concepts for bert-judge-robust-alternative-lexical-methods: 0 matched existing, 1 new, 1 foundation-refs
## [2026-04-16] ingest | added papers/bert-judge-robust-alternative-lexical-methods | updated: concepts/encoder-based-reference-evaluation, claims/encoder-based-evaluation-matches-llm-judge
## [2026-04-16] ingest | concepts for minicheck-efficient-fact-checking-llms-grounding: 2 matched existing (encoder-based-reference-evaluation, atomic-fact-decomposition), 0 new, 1 foundation-refs
## [2026-04-16] ingest | claims for minicheck-efficient-fact-checking-llms-grounding: 1 matched existing (encoder-based-evaluation-matches-llm-judge), 0 new
## [2026-04-16] ingest | added papers/minicheck-efficient-fact-checking-llms-grounding | updated: claims/encoder-based-evaluation-matches-llm-judge (upgraded to supported 0.75), concepts/encoder-based-reference-evaluation (added MiniCheck variant)
## [2026-04-16] ingest | claims for llm-verifier-general-purpose-verification-framework: 0 matched existing, 1 new
## [2026-04-16] ingest | concepts for llm-verifier-general-purpose-verification-framework: 0 matched existing, 1 new concepts, 1 foundation-refs, 0 new foundations, 1 terminology-refs, 0 new terminology
## [2026-04-16] ingest | added papers/llm-verifier-general-purpose-verification-framework | updated: concepts/llm-verifier, claims/logprob-based-multi-criteria-verification-improves, foundations/llm-as-judge (edge only)
## [2026-04-17] ingest | claims for gepa-reflective-prompt-evolution-outperform-reinforcement: 1 matched existing, 2 new
## [2026-04-17] ingest | concepts for gepa-reflective-prompt-evolution-outperform-reinforcement: 1 matched existing (teleprompters), 2 new (reflective-prompt-evolution, compound-ai-system), 1 foundation-ref (prompt-optimization), 0 new foundations, 0 terminology-refs, 0 new terminology
## [2026-04-17] ingest | added papers/gepa-reflective-prompt-evolution-outperform-reinforcement | created: concepts/reflective-prompt-evolution, concepts/compound-ai-system, claims/reflective-prompt-evolution-outperforms-reinforcement-learning, claims/pareto-aware-candidate-selection-prevents-prompt, people/lakshya-agrawal | updated: concepts/teleprompters, claims/dspy-compiled-programs-outperform-hand-crafted-prompts, people/omar-khattab, people/christopher-potts, people/matei-zaharia | edges: 8
## [2026-04-17] edit | deleted 3 deprecated ideas (distilled-rationale/taxonomic/pairwise-preference feedback for Prompt Refiner) | cleaned reverse links in 4 pages + 3 graph edges
