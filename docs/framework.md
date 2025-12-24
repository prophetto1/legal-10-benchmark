# Framework

Legal-10 is an open evaluation framework for legal AI. We open-source everything: datasets, harness, scoring, and operational tooling. Every evaluation produces auditable run bundles with prompt-level transparency—what went in, what came out, how it was scored. Anyone can reproduce the results.

Legal-10 provides a unified interface for evaluating both open-weight and closed API models (GPT-4, Claude, Llama, Gemini) through identical pipelines. Prompts and configurations are standardized across providers, so score differences reflect model capability rather than evaluation artifacts.

## Architecture Foundation

Legal-10 implements the evaluation architecture pioneered by Stanford CRFM's HELM (Holistic Evaluation of Language Models). This architecture provides:

- **Inheritance-based extension** — scenarios, metrics, and adapters extend base classes
- **Decorator-based registration** — run specs auto-register via `@run_spec_function`
- **Factory pattern instantiation** — scenarios and metrics instantiate via class names
- **YAML-based configuration** — models and deployments configured declaratively

This architecture has a proven track record of domain-specific extension—MedHELM adapts it for medical AI across 121 clinician-validated tasks; IBM's enterprise benchmark extends it for finance and legal scenarios.

Legal-10 supplies the legal skill taxonomy, datasets, and scoring rubrics grounded in professional standards. The underlying architecture operates the way we believe legal AI evaluation should work: open, inspectable, reproducible, and resistant to selective reporting.