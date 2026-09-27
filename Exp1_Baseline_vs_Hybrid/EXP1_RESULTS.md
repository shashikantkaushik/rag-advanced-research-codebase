# Experiment 1 — Pilot: Dense vs. Hybrid (60 questions, alpha=0.5)

This was the first, smaller pilot experiment (60 documents, 60 test questions — one
per document, tagged `keyword` or `semantic`), run using `RAG_Exp1_Colab.ipynb`
(dense embeddings: all-MiniLM-L6-v2 + FAISS; hybrid: BM25 + dense, alpha=0.5).

These numbers were reported directly from an actual Colab execution (not a
fabricated/estimated result), but no raw `results.json`/CSV export was saved from this
particular run — only the summary metrics below. Experiment 2 (`Exp2_180Q_AlphaSweep/`)
re-runs the same comparison at a larger scale (180 questions, full alpha sweep) with
complete raw output files, and is the version reported in the paper.

## Dense baseline

| Metric | Value |
|---|---|
| Recall@1 | 0.85 |
| MRR@1 | 0.85 |
| Recall@3 | 0.9667 |
| MRR@3 | 0.90 |
| Keyword Recall@1 | 0.9667 |
| Semantic Recall@1 | 0.7333 |

## Hybrid (alpha=0.5)

| Metric | Value |
|---|---|
| Recall@1 | 0.9167 |
| MRR@1 | 0.9167 |
| Recall@3 | 0.9833 |
| MRR@3 | 0.9472 |
| Keyword Recall@1 | 0.9333 |
| Semantic Recall@1 | 0.90 |

## Relationship to Experiment 2

This pilot's descriptive direction (hybrid > dense) matches Experiment 2's finding at
the larger 180-question scale. Experiment 2 additionally tests this difference for
statistical significance (see `Exp2_180Q_AlphaSweep/` and `Exp3_Reranking_Significance/`)
and finds it is *not* significant at n=180 — a caveat this smaller n=60 pilot does not,
by itself, resolve either way. Report Experiment 2/3's numbers as the primary result;
treat this pilot as preliminary/supporting only.
