# rag-eval-technical-qa

**Dense, Sparse, and Hybrid Retrieval for Domain-Specific RAG: An Evaluation on Synthetic Nginx Technical Documentation**

Author: Shashi Kant Kaushik

A small-scale, honestly-reported RAG retrieval research project: does hybrid
(BM25 + dense embedding) retrieval outperform either method alone on a
technical-documentation domain, and does cross-encoder reranking help further? All
results in this repo come from actually executing the linked Colab notebooks — none
are fabricated or estimated.

📄 **Full paper**: [`paper/RAG_Research_Paper.pdf`](paper/RAG_Research_Paper.pdf) (also available as `.docx`)

## Headline Result

| Method | Recall@1 | Recall@3 | Recall@5 | MRR |
|---|---|---|---|---|
| BM25 only | 50.6% | 69.4% | 79.4% | 0.610 |
| Dense only (all-MiniLM-L6-v2) | 72.2% | 85.6% | 92.8% | 0.801 |
| **Hybrid, alpha=0.75 (best)** | **76.1%** | **91.1%** | **93.3%** | **0.831** |
| Hybrid + Cross-Encoder Rerank | 71.7% | 87.2% | 92.2% | 0.800 |

**Important caveat, reported honestly in the paper**: the Hybrid-vs-Dense improvement
above is *not* statistically significant at n=180 (paired bootstrap 95% CI
[-1.1%, +8.9%], McNemar p=0.19). Cross-encoder reranking shows no overall benefit, but
a category-dependent effect (helps `semantic` questions, hurts `troubleshooting`
questions — see the paper's Discussion section). See `Exp2_180Q_AlphaSweep/` and
`Exp3_Reranking_Significance/` for full detail.

## Repository Structure

```
.
├── data/
│   ├── docs.jsonl              # 60 synthetic Nginx knowledge-base documents (original text)
│   ├── qa_final_180.jsonl      # 180 test questions (60 keyword / 60 semantic / 60 troubleshooting)
│   └── qa_pilot_60.jsonl       # earlier 60-question pilot test set (see Exp1)
│
├── Exp1_Baseline_vs_Hybrid/    # Pilot: dense vs. hybrid (alpha=0.5), 60 questions
│   ├── RAG_Exp1_Colab.ipynb
│   └── EXP1_RESULTS.md
│
├── Exp2_180Q_AlphaSweep/       # Main experiment: BM25 / Dense / Hybrid, full alpha sweep, 180 questions
│   ├── RAG_Exp2_Colab.ipynb
│   ├── EXPERIMENT_PROTOCOL.md  # research question, methods, metrics, limitations
│   ├── final_results.json      # raw output from actually running the notebook
│   ├── alpha_sweep.csv
│   ├── category_breakdown.csv
│   └── error_analysis.csv
│
├── Exp3_Reranking_Significance/ # Cross-encoder reranking + bootstrap/McNemar significance tests
│   ├── RAG_Exp3_Colab.ipynb
│   ├── EXPERIMENT3_ADDENDUM.md
│   ├── experiment3_results.json
│   ├── experiment3_summary.csv
│   └── experiment3_category_breakdown.csv
│
├── paper/
│   ├── RAG_Research_Paper.docx
│   └── RAG_Research_Paper.pdf
│
├── requirements.txt
└── LICENSE
```

## Reproducing the Results

Each `Exp*/RAG_Exp*_Colab.ipynb` is self-contained and runnable in Google Colab:

1. Open the notebook in [Google Colab](https://colab.research.google.com).
2. Run all cells top to bottom.
3. When prompted, upload the relevant file(s) from `data/` (each notebook's first
   markdown cell says exactly which).
4. The notebook saves its own result files (JSON/CSV) at the end — these are the same
   files already committed in each `Exp*/` folder from the original run.

No GPU is required; all three notebooks run on Colab's free CPU tier in a few minutes
each (dominated by downloading `all-MiniLM-L6-v2` / the cross-encoder on first run).

## Method Summary

- **Dataset**: 60 original synthetic Nginx admin documents (not copied from official
  docs) + 180 questions across three categories (keyword / semantic / troubleshooting),
  with category labels programmatically verified against each document's core
  technical term.
- **Retrieval methods**: BM25 (`rank_bm25`), dense (`all-MiniLM-L6-v2` + FAISS
  `IndexFlatL2`), hybrid (min-max normalized linear combination, alpha swept over
  {0, 0.25, 0.5, 0.75, 1}), and hybrid + cross-encoder reranking
  (`ms-marco-MiniLM-L-6-v2`) on a top-10 shortlist.
- **Metrics**: Recall@1/3/5, MRR (computed from the full ranked list, not derived from
  Recall@1).
- **Significance testing**: paired bootstrap (10,000 resamples) + McNemar's test on
  per-query Recall@1 outcomes.

Full detail, related work, discussion, and limitations are in the paper.

## Limitations (see paper Section 7 for full detail)

Single synthetic domain, small n (180 questions), single embedding model and single
reranker, and a documented dataset-level artifact (one document with an unusually broad
false-positive pull across unrelated queries). None of the significant-sounding
descriptive differences reported here have been confirmed at conventional statistical
significance thresholds at this sample size — this is stated plainly in the paper
rather than glossed over.

## Citation

If you use this dataset or code, please cite the accompanying paper (see `paper/`).
A formal BibTeX entry will be added once this work has a DOI (arXiv or otherwise).

## License

Code and synthetic dataset are released under the MIT License (see `LICENSE`). The
dataset is entirely synthetic/original text; no proprietary, confidential, or
copyrighted material (including official Nginx documentation) is included.
