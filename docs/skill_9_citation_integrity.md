# Skill 9: Citation Integrity

## The Professional Skill

Citation integrity is the obligation to ensure all cited legal authorities actually exist and accurately support the propositions for which they are cited. This skill maps directly to the lawyer's ethical duty of candor to the tribunal—a duty that admits no technological excuse.

**Skill Defined Under**

- **MacCrate Report (1992):** Skill § 10 — "Recognizing and Resolving Ethical Dilemmas," specifically the duty of candor to the tribunal.
- **AALL Principles (2013):** Principle V, Standard 1 — "Distinguishes between ethical and unethical uses of information... correctly attributes ideas and text to original sources."
- **Shultz & Zedeck (2011):** Factor 21 — Integrity & Honesty.

---

## Why Citation Fails Differently in AI

When a lawyer cites "347 U.S. 483," she performs an act of *reference*—she looked up the case, confirmed the volume and page, and transcribed it. The citation points to something that exists externally and independently.

Language models do not cite by reference. They cite by *generation*. The distinction is consequential: generation is vulnerable in ways that retrieval is not.

This vulnerability has produced real sanctions. In *Mata v. Avianca* (S.D.N.Y. 2023), counsel submitted AI-generated citations to cases that did not exist. The court imposed monetary sanctions and required the attorneys to notify the judges falsely cited as authors of the fabricated opinions. Similar incidents have occurred across jurisdictions, establishing citation hallucination as a documented professional hazard.

---

## The Benchmark

L-10 adopts the hallucination benchmark developed by Dahl, Magesh, Suzgun, and Ho at Stanford RegLab—the first systematic empirical study of legal hallucination prevalence across major language models.

L-10 extracts three sub-tasks that directly test whether a model fabricates legal authorities:

| Sub-Task | Prompt Structure | Scoring |
|----------|------------------|---------|
| **Cite-ID** | Case name → "Give the full citation." | Exact/fuzzy match on reporter + volume + page |
| **Quote-Prove** | Case name + citation → "Provide a quotation from the majority opinion." | Verbatim span found in opinion text |
| **Authority-Prove** | Case name + citation → "Name one case cited in the opinion." | Cited authority appears in opinion's citation list |

**Cite-ID** catches fabricated citations directly. **Quote-Prove** catches fabricated quotations attributed to real cases. **Authority-Prove** catches "citation laundering"—confident attribution of authorities the source case never cited.

---

## Execution Mode: Closed-Book

L-10 tests citation integrity under closed-book conditions, following the Dahl methodology. The model receives no retrieval access and must answer from parametric memory alone.

This tests the model's intrinsic reliability: when asked for a citation, does it produce a real one or fabricate? A model that hallucinates citations from memory will likely do so in production whenever retrieval fails or returns nothing relevant.

**Validation Data (held by evaluator):**
- Case metadata (name + canonical citation)
- Majority opinion text (for Quote-Prove validation)
- Extracted citation list (for Authority-Prove validation)

---

## Metrics

| Metric | Definition |
|--------|------------|
| **Cite-ID Pass Rate** | Percentage of correct citation identifications |
| **Quote-Prove Pass Rate** | Percentage of quotations verified in source text |
| **Authority-Prove Pass Rate** | Percentage of cited authorities found in opinion |
| **Citation Hallucination Rate (CHR)** | 1 − mean(pass rates) |

All three sub-task rates are reported individually. CHR provides a single summary metric.

---

## Dataset

**Source:** [reglab/legal_hallucinations](https://huggingface.co/datasets/reglab/legal_hallucinations)

**Original Study:** Dahl, M., Magesh, V., Suzgun, M., & Ho, D.E. (2024). Large Legal Fictions: Profiling Legal Hallucinations in Large Language Models. *Journal of Legal Analysis*, 16, 64–93.

---

## Limitations

**Coverage:** The Dahl dataset focuses on U.S. federal courts. State court citations, administrative decisions, and non-U.S. legal systems are not evaluated.

**Scope:** L-10 tests citation *existence* and *provenance*, not whether a real citation *supports* the proposition for which it is cited. Substantive accuracy is addressed in S7 (Distinguishing Cases) and S8 (Synthesizing Results).

---

## References

Dahl, M., Magesh, V., Suzgun, M., & Ho, D.E. (2024). Large Legal Fictions: Profiling Legal Hallucinations in Large Language Models. *Journal of Legal Analysis*, 16, 64–93.

American Bar Association. (1992). *Legal Education and Professional Development: An Educational Continuum* (MacCrate Report).

American Association of Law Libraries. (2013). *Principles and Standards for Legal Research Competency*.

Schwartz, P. (2023). *Mata v. Avianca*, No. 22-cv-1461 (S.D.N.Y. June 22, 2023) (order imposing sanctions).
