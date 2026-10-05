# Experiment 4 Addendum: Adaptive Retrieval Router

## Motivation
Experiments 2–3 showed no single fixed strategy is best for every query type: dense
alone is already perfect on keyword queries, hybrid is best on troubleshooting queries,
and reranking helps semantic queries but hurts troubleshooting queries. This motivates
a **router** that picks a strategy per query instead of applying one strategy globally.

## Method
A transparent, rule-based router (no ML training) classifies each query using two
signals: presence of an HTTP status code or underscore-joined directive-like token
(→ keyword), and query length (≥14 tokens → troubleshooting). Each predicted category
maps to a fixed retrieval strategy (keyword→dense, troubleshooting→hybrid,
semantic→hybrid+rerank).

## Evaluation Design
1. **Oracle upper bound**: for each query, the best of the three base methods
   (in hindsight) defines a ceiling the router cannot exceed. This isolates "router
   design headroom" from "base method quality."
2. **Router classification accuracy**: a confusion matrix against the dataset's real
   category labels, evaluated independently of retrieval accuracy.
3. **Cost-accuracy comparison**: each method is assigned an approximate relative
   compute cost (dense/hybrid ≈ 1 embedding+BM25 call; hybrid+rerank ≈ that plus 10
   cross-encoder passes on the shortlist), so the router's value is judged on accuracy
   *and* efficiency, not accuracy alone.
4. **Significance testing**: paired bootstrap + McNemar's test, router vs. the best
   single fixed strategy, identical methodology to Experiments 2–3.
5. **Ablation**: the keyword-regex signal and the length signal are evaluated alone and
   combined, to isolate which one drives any improvement.
6. **Error analysis**: retrieval accuracy is computed separately for queries the router
   classifies correctly vs. incorrectly, isolating classification error from retrieval
   error.

## What Must Come From Actual Colab Execution
All numbers (oracle bound, confusion matrix, comparison table, significance results,
ablation, error analysis) must come from running `RAG_Experiment4_Colab.ipynb`. If the
router does not beat the best fixed strategy, or the improvement is not statistically
significant, that must be reported as the actual finding — the oracle gap and ablation
results are diagnostic regardless of the headline outcome.
