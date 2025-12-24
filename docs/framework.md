# Framework

Legal-10 uses Stanford CRFM's Holistic Evaluation of Language Models (HELM) as its evaluation infrastructure. HELM is fully open-source: framework, datasets, scoring, and tooling are all publicly available. Every evaluation produces auditable run bundles with prompt-level transparency—what went in, what came out, how it was scored. Anyone can reproduce the results.

HELM provides a unified interface for evaluating both open-weight and closed API models (GPT-4, Claude, Llama, Gemini) through identical pipelines. Prompts and configurations are standardized across providers, so score differences reflect model capability rather than evaluation artifacts.

The framework has a proven track record of domain-specific extension—MedHELM adapts it for medical AI across 121 clinician-validated tasks; IBM's enterprise benchmark extends it for finance and legal scenarios. Legal-10 follows the same pattern: HELM supplies the evaluation machinery, we supply the legal skill taxonomy, datasets, and scoring rubrics grounded in professional standards.

We chose HELM because it already operates the way we believe legal AI evaluation should work: open, inspectable, reproducible, and resistant to selective reporting.