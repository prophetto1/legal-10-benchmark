# Mission Statement

Legal AI is already being used to make decisions that affect liberty, employment, housing, immigration status, family stability, and access to counsel. Today's evaluation ecosystem is fragmented, vendor-permissioned, and often optimized for optics rather than end-user protection.

**Legal-10 exists to change that.**

Legal-10 is an open, skill-based benchmark suite designed to make legal AI performance inspectable, comparable, and auditable—across three delivery modalities: Closed-Book (CB), Retrieval-Augmented Generation (RAG), and Agentic Workflows (AG).

We open-source everything: datasets, harness, scoring, and operational tooling. The only exception is the actual administered test data, which remains private to prevent contamination and prior knowledge.

## Design Principles

### Objectivity
- Fixed spec per version — changes bump the version
- No selective omission or quiet withdrawal — full eval split or no score

### Transparency
- Open-source harness — datasets, scoring, tools
- Auditable run bundles — SHA-256 hashes, signed manifests
- Public append-only log — all submissions preserved

### Fairness
- Comparable baselines — open and closed models use identical harness
- Modality-matched testing — CB vs RAG vs AG reflects real lawyer workflows

### Gaming-Resistant
- No selective omission → must run all 10 skills
- Skill-level reporting → can't hide weak skills in aggregate
- Visible failure modes → can't hide how it failed

## Community-Driven

Legal-10 is community-governed, not vendor-controlled. We ask for your help in pushing this benchmark initiative. We need a fair and comprehensive benchmark that end users can rely on to better understand the quality implications of a product they are using or considering.

**Benefit** 

Co-author credit on papers, governance voting rights, contributor recognition