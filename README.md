# rag-eval-technical-qa

**RAG Retrieval Research: Dense, Sparse, Hybrid, Query Routing, and Beyond**

Author: **Shashi Kant Kaushik** — [ORCID: 0009-0008-1559-1209](https://orcid.org/0009-0008-1559-1209)

A small-scale, honestly-reported RAG retrieval research codebase covering three papers:
(1) dense vs. sparse vs. hybrid retrieval on a technical-documentation domain, (2) how much
query routing helps hybrid retrieval across multiple benchmarks, and (3) ongoing work on
advanced RAG techniques. All results in this repo come from actually executing the linked
Colab notebooks — none are fabricated or estimated.

## 📚 Published Papers

### 📄 Paper 1 — Dense, Sparse, and Hybrid Retrieval for Domain-Specific RAG
**An Evaluation on Synthetic Nginx Technical Documentation**

- **Status**: Preprint (published 2026-09-27)
- **DOI**: [10.5281/ZENODO.22996163](https://doi.org/10.5281/ZENODO.22996163)
- **Experiments**: `Exp1_Baseline_vs_Hybrid/`, `Exp2_180Q_AlphaSweep/`, `Exp3_Reranking_Significance/`
- **Paper files**: [`paper/RAG_Research_Paper.pdf`](paper/RAG_Research_Paper.pdf) (also `.docx`)

| Method | Recall@1 | Recall@3 | Recall@5 | MRR |
|---|---|---|---|---|
| BM25 only | 50.6% | 69.4% | 79.4% | 0.610 |
| Dense only (all-MiniLM-L6-v2) | 72.2% | 85.6% | 92.8% | 0.801 |
| **Hybrid, alpha=0.75 (best)** | **76.1%** | **91.1%** | **93.3%** | **0.831** |
| Hybrid + Cross-Encoder Rerank | 71.7% | 87.2% | 92.2% | 0.800 |

**Important caveat**: the Hybrid-vs-Dense improvement above is *not* statistically
significant at n=180 (paired bootstrap 95% CI [-1.1%, +8.9%], McNemar p=0.19).
Cross-encoder reranking shows no overall benefit, but a category-dependent effect
(helps `semantic` questions, hurts `troubleshooting` questions). See
`Exp2_180Q_AlphaSweep/` and `Exp3_Reranking_Significance/` for full detail.

### 📄 Paper 2 — How Much Can Query Routing Help Hybrid Retrieval?
**A Multi-Benchmark Study with Latency Analysis**

- **Status**: Preprint (published 2026-10-03)
- **DOI**: [10.5281/ZENODO.23114659](https://doi.org/10.5281/ZENODO.23114659)
- **Experiments**: `Exp4_Query_Expansion/`, `Exp5_HyDE/`, `Exp6_Multi_Query/`,
  `Exp7_Parent_Document_Retriever/`, `Exp8_Contextual_Compression/`

### 🔄 Paper 3 — (Work in Progress)
**Advanced RAG techniques — currently running**

- **Status**: 🔄 Running
- **Experiments**: `Exp9_Self_RAG/`, `Exp10_GraphRAG/`
- Results will be added as they become available.

## Repository Structure

```
.
├── data/
│   ├── docs.jsonl              # 60 synthetic Nginx knowledge-base documents (original text)
│   ├── qa_final_180.jsonl      # 180 test questions (60 keyword / 60 semantic / 60 troubleshooting)
│   └── qa_pilot_60.jsonl       # earlier 60-question pilot test set (see Exp1)
│
│ ─── Paper 1 ────────────────────────────────────────────────
├── Exp1_Baseline_vs_Hybrid/         # Pilot: dense vs. hybrid (alpha=0.5), 60 questions
│   ├── RAG_Exp1_Colab.ipynb
│   └── EXP1_RESULTS.md
│
├── Exp2_180Q_AlphaSweep/            # Main experiment: BM25 / Dense / Hybrid, full alpha sweep, 180 questions
│   ├── RAG_Exp2_Colab.ipynb
│   ├── EXPERIMENT_PROTOCOL.md
│   ├── final_results.json
│   ├── alpha_sweep.csv
│   ├── category_breakdown.csv
│   └── error_analysis.csv
│
├── Exp3_Reranking_Significance/     # Cross-encoder reranking + bootstrap/McNemar significance tests
│   ├── RAG_Exp3_Colab.ipynb
│   ├── EXPERIMENT3_ADDENDUM.md
│   ├── experiment3_results.json
│   ├── experiment3_summary.csv
│   └── experiment3_category_breakdown.csv
│
│ ─── Paper 2 ────────────────────────────────────────────────
├── Exp4_Query_Expansion/            # Query expansion experiments
├── Exp5_HyDE/                       # Hypothetical Document Embeddings
├── Exp6_Multi_Query/                # Multi-query retrieval
├── Exp7_Parent_Document_Retriever/  # Parent-document retrieval
├── Exp8_Contextual_Compression/     # Contextual compression
│
│ ─── Paper 3 (running) ─────────────────────────────────────
├── Exp9_Self_RAG/                   # Self-reflective RAG
├── Exp10_GraphRAG/                  # Graph-based RAG
│
├── paper/
│   ├── RAG_Research_Paper.docx
│   └── RAG_Research_Paper.pdf
│
├── requirements.txt
└── LICENSE
```

## Experiments Overview

| Exp | Name | Folder | Paper | Status |
|---|---|---|---|---|
| Exp1 | Baseline vs Hybrid (pilot) | `Exp1_Baseline_vs_Hybrid/` | Paper 1 | ✅ Published |
| Exp2 | 180Q Alpha Sweep | `Exp2_180Q_AlphaSweep/` | Paper 1 | ✅ Published |
| Exp3 | Reranking Significance | `Exp3_Reranking_Significance/` | Paper 1 | ✅ Published |
| Exp4 | Query Expansion | `Exp4_Query_Expansion/` | Paper 2 | ✅ Published |
| Exp5 | HyDE | `Exp5_HyDE/` | Paper 2 | ✅ Published |
| Exp6 | Multi-Query Retrieval | `Exp6_Multi_Query/` | Paper 2 | ✅ Published |
| Exp7 | Parent Document Retriever | `Exp7_Parent_Document_Retriever/` | Paper 2 | ✅ Published |
| Exp8 | Contextual Compression | `Exp8_Contextual_Compression/` | Paper 2 | ✅ Published |
| Exp9 | Self-RAG | `Exp9_Self_RAG/` | Paper 3 | 🔄 Running |
| Exp10 | GraphRAG | `Exp10_GraphRAG/` | Paper 3 | 🔄 Running |

## Reproducing the Results

Each `Exp*/RAG_Exp*_Colab.ipynb` is self-contained and runnable in Google Colab:

1. Open the notebook in [Google Colab](https://colab.research.google.com).
2. Run all cells top to bottom.
3. When prompted, upload the relevant file(s) from `data/` (each notebook's first
   markdown cell says exactly which).
4. The notebook saves its own result files (JSON/CSV) at the end — these are the same
   files already committed in each `Exp*/` folder from the original run.

No GPU is required; all notebooks run on Colab's free CPU tier in a few minutes
each (dominated by downloading `all-MiniLM-L6-v2` / the cross-encoder on first run).

## Method Summary

- **Dataset**: 60 original synthetic Nginx admin documents (not copied from official
  docs) + 180 questions across three categories (keyword / semantic / troubleshooting),
  with category labels programmatically verified against each document's core
  technical term.
- **Retrieval methods**: BM25 (`rank_bm25`), dense (`all-MiniLM-L6-v2` + FAISS
  `IndexFlatL2`), hybrid (min-max normalized linear combination, alpha swept over
  {0, 0.25, 0.5, 0.75, 1}), hybrid + cross-encoder reranking
  (`ms-marco-MiniLM-L-6-v2`) on a top-10 shortlist, and advanced techniques
  (query expansion, HyDE, multi-query, parent-document retrieval, contextual
  compression, self-RAG, GraphRAG).
- **Metrics**: Recall@1/3/5, MRR (computed from the full ranked list, not derived from
  Recall@1).
- **Significance testing**: paired bootstrap (10,000 resamples) + McNemar's test on
  per-query Recall@1 outcomes.

Full detail, related work, discussion, and limitations are in the respective papers.

## Limitations

Single synthetic domain (Paper 1), small n (180 questions), single embedding model and
single reranker, and a documented dataset-level artifact (one document with an unusually
broad false-positive pull across unrelated queries). None of the significant-sounding
descriptive differences reported here have been confirmed at conventional statistical
significance thresholds at this sample size — this is stated plainly in the papers
rather than glossed over.

## Citation

If you use this dataset or code, please cite the accompanying papers:

```bibtex
@misc{kaushik2026densesparsehybrid,
  author       = {Kaushik, Shashi Kant},
  title        = {Dense, Sparse, and Hybrid Retrieval for Domain-Specific RAG: An Evaluation on Synthetic Nginx Technical Documentation},
  year         = {2026},
  doi          = {10.5281/ZENODO.22996163},
  note         = {Preprint}
}

@misc{kaushik2026queryrouting,
  author       = {Kaushik, Shashi Kant},
  title        = {How Much Can Query Routing Help Hybrid Retrieval? A Multi-Benchmark Study with Latency Analysis},
  year         = {2026},
  doi          = {10.5281/ZENODO.23114659},
  note         = {Preprint}
}
```

## License

Code and synthetic dataset are released under the MIT License (see `LICENSE`). The
dataset is entirely synthetic/original text; no proprietary, confidential, or
copyrighted material (including official Nginx documentation) is included.
