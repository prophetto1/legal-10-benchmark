# Legal-10 Development Notes

## Dataset Availability Status

### ✓ Available on HuggingFace
- **CUAD** - [theatticusproject/cuad-qa](https://huggingface.co/datasets/theatticusproject/cuad-qa) (S6: Fact Extraction)
- **CLERC** - [jhu-clsp/CLERC](https://huggingface.co/datasets/jhu-clsp/CLERC) (S3, S4, S5: Citation/Authority tasks)
- **LEXam** - [LEXam-Benchmark/LEXam](https://huggingface.co/datasets/LEXam-Benchmark/LEXam) (S8: Synthesize Results)
- **FairLex** - [coastalcph/fairlex](https://huggingface.co/datasets/coastalcph/fairlex) (Extension: Multilingual)
- **CaseHOLD** - [casehold/casehold](https://huggingface.co/datasets/casehold/casehold) (S7: Distinguish Cases) - Already in HELM

### ⚠ Available on GitHub
- **Dahl** - [reglab/legal_hallucinations](https://github.com/reglab/legal_hallucinations) (S9: Citation Integrity)
- **LegalAgentBench** - [CSHaitao/LegalAgentBench](https://github.com/CSHaitao/LegalAgentBench) (S1, S2: Agentic) - Chinese-focused

### ❌ Not Publicly Available
- **SHIELD** - Dataset not public due to copyright concerns (S10: Copyright Compliance)
  - Paper: [arXiv:2406.12975](https://arxiv.org/abs/2406.12975)

## Existing HELM Implementations

### Already Implemented
- **CaseHOLD** - `src/helm/benchmark/scenarios/casehold_scenario.py`
  - ~53k questions, 5-choice MC
  - Run spec: `enterprise_run_specs.py::get_casehold_spec()`

- **LegalBench** - `src/helm/benchmark/scenarios/legalbench_scenario.py`
  - ~100 instances per subset (5 subsets)
  - Run spec: `lite_run_specs.py::get_legalbench_spec(subset)`

### To Implement (8 datasets)
1. CLERC (S3, S4, S5)
2. CUAD (S6)
3. LEXam (S8)
4. LegalAgentBench (S1, S2)
5. KeyCite (S5) - unclear if separate or part of CLERC
6. SHIELD (S10)
7. Dahl (S9)
8. FairLex (Extension)

## Implementation Patterns (from CaseHOLD & LegalBench)

### Dataset Loading
- Use HuggingFace `load_dataset()` with `cache_dir` parameter
- Cache to `output_path/data/`
- Support train/test splits

### Instance Structure
```python
Instance(
    input=Input(text="..."),
    references=[Reference(Output(text="..."), tags=["correct" or []])],
    split="train" or "test",
    id=f"id{unique_id}"
)
```

### Run Spec Pattern
- Few-shot: 2-5 examples
- Metrics: `get_exact_match_metric_specs()`
- Adapt methods: `ADAPT_MULTIPLE_CHOICE_JOINT` or `ADAPT_GENERATION`

## Computational Estimates

### Per-Dataset (based on CaseHOLD/LegalBench)
- **CaseHOLD**: ~1200-1850 tokens/instance, 2 few-shot examples
- **LegalBench**: ~650-1250 tokens/instance, 5 few-shot examples
- **Estimated runtime**: 30-60 min for 1 model across all 10 skills (~1000-1500 test instances)

## Branding Changes Completed
- [x] README.md updated with Legal-10 mission statement
- [x] CITATION.bib updated (author: Jon Chung)
- [ ] pyproject.toml (package name, description, author)
- [ ] LICENSE (copyright holder)
- [ ] Frontend branding

## Next Steps
1. Decide on test set sampling strategy (all datasets vary in size)
2. Create `legal10_run_specs.py` for all 10 skills
3. Implement missing scenario files
4. Handle SHIELD alternative (S10)
5. Handle LegalAgentBench language issue (S1, S2)
