# Legal-10 Design Principles

## Overview

Legal-10 is a skill-based benchmark for evaluating legal AI systems. This document describes the design principles that guide its development.

---

## Design Principles

### 1. Objectivity

**Fixed Specification**
- Each version has a fixed specification
- Changes to the specification require a version increment
- Evaluations specify which version was used

**Complete Evaluation**
- All 10 skills must be evaluated
- No partial scores or selective omission
- If a skill cannot be evaluated, this is explicitly noted

**Versioning**
- Version numbers follow semantic versioning
- Breaking changes increment major version
- Specification changes are documented in changelog

---

### 2. Transparency

**Open Source**
- Evaluation harness is open source
- Datasets are publicly accessible or documented
- Scoring implementations are visible
- Run specifications are published

**Reproducibility**
- Evaluations include SHA-256 hashes
- Dataset versions are pinned
- Full run bundles are preserved
- Evaluation conditions are documented

**Public Logging**
- Submissions are logged with timestamps
- Historical performance is preserved
- Append-only log prevents silent deletions

---

### 3. Fairness

**Standardized Evaluation**
- All models use identical evaluation protocol
- Same prompts, same datasets, same metrics
- No model-specific optimizations in core harness

**Modality-Matched Testing**
- Closed-book tasks (S7-S10): No retrieval allowed
- RAG tasks (S3-S6): Retrieval capability tested
- Agentic tasks (S1-S2): Multi-step reasoning evaluated
- Testing matches intended use patterns

**Comparable Baselines**
- Open-weight models use same harness as API models
- Local deployments use same evaluation as hosted services

---

### 4. Gaming Resistance

**No Selective Omission**
- Cannot cherry-pick favorable tasks
- All 10 skills required for score reporting
- Partial evaluation results are not published

**Granular Reporting**
- Skill-level scores reported individually
- Aggregate score does not hide weak performance
- Per-instance results available for analysis

**Multiple Evaluation Axes**
- Tests across different modalities (CB/RAG/AG)
- Multiple metrics per skill where appropriate
- Cannot optimize for single metric

---

## Professional Standards Grounding

Legal-10 skills map to established professional competency frameworks:

**MacCrate Report (ABA, 1992)**
- Foundational lawyering skills framework
- Research planning, fact-finding, legal analysis

**AALL Principles (2013)**
- Legal research competency standards
- Authority identification and validation

**Shultz & Zedeck (2011)**
- Empirically-derived lawyer effectiveness factors
- Strategic planning, practical judgment

These frameworks represent 30+ years of validated professional standards.

---

## Skill Definitions

Legal-10 evaluates 10 distinct skills:

| Skill | Name | Modality |
|-------|------|----------|
| S1 | Research Planning | Agentic (AG) |
| S2 | Strategic Stopping | Agentic (AG) |
| S3 | Known Authority | RAG |
| S4 | Unknown Authority | RAG |
| S5 | Validate Authority | RAG |
| S6 | Fact Extraction | RAG |
| S7 | Distinguish Cases | Closed-Book (CB) |
| S8 | Synthesize Results | Closed-Book (CB) |
| S9 | Citation Integrity | Closed-Book (CB) |
| S10 | Copyright Compliance | Closed-Book (CB) |

Each skill is tested using the modality that most closely matches how human lawyers perform that skill in practice.

---

## Scoreboard Policy

### Participation

- Evaluation is open to any model
- Participation is voluntary
- Vendors may submit their own evaluations
- Community members may evaluate any accessible model

### "Evaluated" List

Systems that have completed full evaluation (all 10 skills) are listed with:
- Model identifier
- Evaluation date
- Version tested
- Link to run bundle

### "Not Yet Evaluated" List

Systems that have not been evaluated are listed neutrally with status tags:
- "No public endpoint" - Model not publicly accessible
- "Access restricted" - Requires special access or licensing
- "Awaiting community submission" - Public but not yet evaluated

This ensures visibility of coverage gaps.

---

## What Legal-10 Measures

**Behavioral Performance**
- How models respond to standardized legal tasks
- Performance across 10 professional skill categories
- Comparative capabilities under identical conditions

**Skill-Specific Capabilities**
- Research planning quality
- Authority identification accuracy
- Citation integrity
- Fact extraction precision
- Legal reasoning capability

---

## What Legal-10 Does Not Measure

**Architectural Parameters**
- Model size, precision, or quantization
- Training data composition or provenance
- Deployment infrastructure
- Version or update history

**Production Reliability**
- Real-world usage patterns
- Domain-specific edge cases
- Integration with specific workflows
- Long-term consistency

**Fitness for Specific Use Cases**
- Deployment safety for particular applications
- Compliance with jurisdiction-specific requirements
- Appropriateness for high-stakes decisions

---

## Limitations

Legal-10 has known limitations:

**Vendor Architecture Opacity**
- Cannot verify model parameters vendors claim
- Cannot detect silent model version changes
- Cannot inspect training data
- Cannot confirm deployment configuration

**Test Coverage**
- Tests standardized workflows, not all possible use patterns
- Cannot evaluate vendor-specific features
- Cannot test every prompt engineering approach
- Evaluates API access, not internal capabilities

**Deployment Context**
- Benchmark performance ≠ production performance
- Controlled evaluation ≠ real-world reliability
- Standardized prompts ≠ expert usage

**Professional Judgment**
- Scores inform decisions, do not make them
- Domain expertise required for fitness assessment
- Ethical obligations remain with deployers

---

## Technical Implementation

### Framework

Legal-10 implements the evaluation architecture pioneered by Stanford CRFM's HELM (Holistic Evaluation of Language Models):

- Unified model interface for open and closed models
- Standardized adapters for consistent prompting
- Reproducible evaluation with auditable run bundles
- Open-source implementation

### Dataset Sources

Datasets are either:
- Publicly available on HuggingFace with documented IDs
- Downloadable from documented URLs with version hashes
- Reproducible from published sources with clear methodology

### Evaluation Process

1. Model receives standardized prompt
2. Response is generated under specified conditions
3. Output is scored using defined metrics
4. Results are aggregated across instances
5. Skill-level and aggregate scores are reported

---

## Governance

**Community Governance**
- Not controlled by any single vendor
- Contributors receive co-author credit on publications
- Community members have governance voting rights
- Transparent decision-making process

**Change Process**
- Specification changes are proposed publicly
- Community review period before adoption
- Breaking changes require major version increment
- All changes documented in changelog

---

## Use Cases

Legal-10 is designed to support:

**Comparative Evaluation**
- Fair comparison across different models
- Standardized measurement of legal capabilities
- Identification of relative strengths and weaknesses

**Quality Assessment**
- Skill-specific performance visibility
- Detection of capability gaps
- Baseline competency verification

**Informed Decision-Making**
- Input for deployment decisions (not substitute for testing)
- Risk assessment for legal AI adoption
- Vendor claim verification (within limitations)

**Research**
- Reproducible benchmarking for academic studies
- Tracking of legal AI capability development
- Identification of areas needing improvement

---

## What Users Should Know

**Legal-10 Provides:**
- Standardized comparison under identical conditions
- Skill-level performance visibility
- Grounding in professional standards
- Open, auditable evaluation methodology

**Legal-10 Does Not Provide:**
- Certification of deployment safety
- Guarantee of production reliability
- Replacement for domain-specific testing
- Verification of vendor architectural claims

**Appropriate Use:**
- As one input among many for deployment decisions
- To identify areas requiring additional testing
- To compare relative capabilities under controlled conditions
- To assess baseline competency across professional skills

**Inappropriate Use:**
- As sole basis for high-stakes deployment
- As substitute for professional judgment
- As certification of legal compliance
- As verification of vendor claims about architecture

---

## Relationship to Other Benchmarks

Legal-10 differs from some existing AI benchmarks in specific ways:

**Participation Model**
- Legal-10: Open evaluation, anyone can run
- Some benchmarks: Vendor opt-in required

**Result Reporting**
- Legal-10: Append-only log, historical performance preserved
- Some benchmarks: Results may be withdrawn or updated

**Task Selection**
- Legal-10: All 10 skills required
- Some benchmarks: May allow selective task participation

**Methodology Transparency**
- Legal-10: Open-source harness, public datasets
- Some benchmarks: May use proprietary test sets

These are factual differences, not claims of superiority.

---

## Future Development

Legal-10 is designed to evolve:

**Potential Extensions**
- Multilingual legal reasoning (in development)
- Additional professional skills
- Cross-jurisdictional evaluation
- Specialized legal domain testing

**Community Input**
- Suggestions for new skills
- Dataset contributions
- Metric improvements
- Framework enhancements

**Version Progression**
- Regular updates to datasets
- Metric refinements based on empirical analysis
- New skills as legal AI capabilities expand

---

## Citation

When referencing Legal-10, please include:
- Benchmark name and version
- Evaluation date
- Link to this documentation
- Dataset sources used

---

*Legal-10 is an open benchmark. We welcome scrutiny, replication, and improvement suggestions.*
