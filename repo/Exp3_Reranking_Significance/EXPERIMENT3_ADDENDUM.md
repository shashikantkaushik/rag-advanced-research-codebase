# Experiment 3 Addendum: Reranking + Significance Testing

Builds on Experiment 2's best config (Hybrid, alpha=0.75, chosen from the alpha sweep
in Experiment 2 — not re-tuned here, to avoid double-dipping on the same test set).

## New Method: Cross-Encoder Reranking
- Hybrid (alpha=0.75) retrieves a top-10 candidate shortlist per query.
- `cross-encoder/ms-marco-MiniLM-L-6-v2` scores each (query, candidate document) pair
  directly (not via separate embeddings), producing a more accurate but more expensive
  relevance score.
- The top-10 shortlist is re-sorted by cross-encoder score; Recall@1/3/5 and MRR are
  recomputed on this reranked list.
- This is a standard three-stage pipeline (sparse+dense retrieval, then cross-encoder
  rerank), so the comparison sits on established methodological ground.

## New Analysis: Statistical Significance
Two paired tests on the 180 per-query Recall@1 outcomes, for two comparisons
(Hybrid vs. Dense-only, and Hybrid+Rerank vs. Hybrid):
- **Paired bootstrap** (10,000 resamples): 95% CI on the Recall@1 difference, plus a
  one-sided empirical p-value.
- **McNemar's test**: tests whether the two methods' disagreements (one right, one
  wrong on the same question) are significantly lopsided.

If a comparison is not statistically significant at n=180, this must be reported as-is
— it does not invalidate the descriptive result, but changes how strongly it can be
claimed in the paper (e.g. "a suggestive but not statistically significant improvement"
rather than "X improves retrieval").

## What Must Come From Actual Colab Execution
All numbers in Experiment 3 (Recall@1/3/5, MRR for the reranked method, both
significance tests' statistics and p-values, and the category breakdown) must come from
running `RAG_Experiment3_Colab.ipynb`. Nothing here specifies or implies the outcome.
