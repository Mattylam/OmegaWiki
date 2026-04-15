# Query Pack (general)

_Auto-generated compressed context. Do not edit._

## Claims (16 total)
- [weakly_supported] Bootstrapping produces better few-shot demonstrations than hand-written examples (conf: 0.65)
- [supported] Integrating LLMs with deterministic symbolic solvers materially improves faithfulness of logical reasoning over LLM-only baselines (conf: 0.85)
- [supported] Natural-language policy and compliance rules can be formalised into SMT-LIB constraints with LLM + human assistance, enabling runtime enforcement that reduces violations without sacrificing task accuracy (conf: 0.75)
- [supported] Rank correlation alone is insufficient for evaluating LLM judges; per-item agreement (Cohen's κ) relative to the inter-human distribution is required (conf: 0.8)
- [weakly_supported] Decomposer quality can be measured automatically via completeness, correctness, and semantic-entropy metrics, enabling decomposer benchmarking and fine-tuning without human annotation (conf: 0.65)
- [supported] Decomposing long-form generation into atomic facts enables fine-grained factuality evaluation that can be automated to within 2% of human agreement (conf: 0.8)
- [weakly_supported] Routing LLM autoformalization through a domain-specific intermediate DSL (with typed interpreter) materially improves translation robustness and auditability over direct LLM-to-solver generation (conf: 0.7)
- [supported] Atomic-fact-based factuality metrics (FActScore and kin) are materially sensitive to the decomposition method used; decomposer is a first-class pipeline component that must be independently calibrated (conf: 0.8)
- [supported] LLM judges exhibit systematic biases across 12 distinct categories, with robustness varying substantially across models and bias types (conf: 0.75)
- [weakly_supported] Extending SMT compliance verification with a minimal-modification repair step converts binary verdicts into actionable compliance diagnostics that localise which specific facts drive non-compliance (conf: 0.65)
- [weakly_supported] Multi-agent debate evaluation outperforms single-agent LLM
## Open Gaps
_Auto-generated open questions. Do not edit._
- [claim/bootstrapping-produces-better-demonstrations-than-hand-written] Bootstrapping produces better few-shot demonstrations than hand-written examples (status: weakly_supported)
- [claim/continual-learning-via-external-memory] Continual learning for LLM agents can be achieved via external memory evolution without parameter updates (status: weakly_supported)
- [claim/decomposer-quality-can-be-measured-automatically] Decomposer quality can be measured automatically via completeness, correctness, and semantic-entropy metrics, enabling decomposer benchmarking and fine-tuning without human annotation (status: weakly_supported)
- [claim/dsl-mediated-autoformalization-improves-translation-robustness] Routing LLM autoformalization through a domain-specific intermediate DSL (with typed interpreter) materially improves translation robustness and auditability over direct LLM-to-solver generation (status: weakly_supported)
- [claim/dspy-compiled-programs-outperform-hand-crafted-prompts] Compiled DSPy programs outperform hand-crafted prompt chains (status: weakly_supported)
- [claim/minimal-modification-smt-repair-enables-actionable-compliance-diagnostics] Extending SMT compliance verification with a minimal-modification repair step converts binary verdicts into actionable compliance diagnostics that localise which specific facts drive non-compliance (status: weakly_supported)
- [claim/multi-agent-debate-outperforms-single-agent-judge] Mult
## Papers (15 total)
- [5] DSPy: Compiling Declarative Language Model Calls into Self-Improving Pipelines (NLP)
- [4] When AIs Judge AIs: The Rise of Agent-as-a-Judge Evaluation for LLMs (NLP)
- [4] Memento-Skills: Let Agents Design Agents (NLP)
- [3] Neuro-Symbolic Compliance: Integrating LLMs and SMT Solvers for Automated Financial Legal Analysis (NLP)
- [3] Solver-Aided Verification of Policy Compliance in Tool-Augmented LLM Agents (NLP)
- [3] DecMetrics: Structured Claim Decomposition Scoring for Factually Consistent LLM Outputs (NLP)
- [4] Proof of Thought: Neurosymbolic Program Synthesis allows Robust and Interpretable Reasoning (NLP)
- [4] A Closer Look at Claim Decomposition (NLP)
- [5] FActScore: Fine-grained Atomic Evaluation of Factual Precision in Long Form Text Generation (NLP)
- [4] Judge's Verdict: A Comprehensive Analysis of LLM Judge Capability Through Human Agreement (NLP)
- [4] Justice or Prejudice? Quantifying Biases in LLM-as-a-Judge (NLP)
- [5] Logic-LM: Empowering Large Language Models with Symbolic Solvers for Faithful Logical Reasoning (NLP)
- [3] Am I More Pointwise or Pairwise? Revealing Position Bias in Rubric-Based LLM-as-a-Judge (NLP)
- [4] Judging the Judges: A Systematic Study of Position Bias in LLM-as-a-Judge (NLP)
- [4] Self-Preference Bias in LLM-as-a-Judge (NLP)
## Recent Relationships (109 total)
  papers/proof-thought-neurosymbolic-program-synthesis --extends--> papers/logic-lm-empowering-llms-symbolic-solvers
  papers/proof-thought-neurosymbolic-program-synthesis --supports--> claims/dsl-mediated-autoformalization-improves-translation-robustness
  papers/proof-thought-neurosymbolic-program-synthesis --supports--> claims/llm-solver-integration-improves-faithful-reasoning
  papers/solver-aided-verification-policy-compliance-tool --derived_from--> foundations/llm-as-judge
  papers/solver-aided-verification-policy-compliance-tool --supports--> concepts/neurosymbolic-llm-verification
  papers/solver-aided-verification-policy-compliance-tool --supports--> concepts/autoformalization
  papers/solver-aided-verification-policy-compliance-tool --supports--> concepts/policy-compliance-smt-formalization
  papers/solver-aided-verification-policy-compliance-tool --supports--> claims/policy-rules-can-be-formalized-as-smt-constraints
  papers/solver-aided-verification-policy-compliance-tool -
