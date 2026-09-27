# Experiment Protocol: Dense vs. BM25 vs. Hybrid Retrieval for Domain-Specific RAG

## Research Question
For a technical, jargon-heavy domain (Nginx system administration), does combining
BM25 keyword search with dense embedding retrieval (hybrid) improve retrieval accuracy
over either method alone, and if so, where — specifically, does any improvement
concentrate on queries that reuse exact technical terms (directive names, HTTP status
codes), as opposed to paraphrased or multi-concept troubleshooting queries?

This is treated as an open empirical question, not a predetermined conclusion. A result
showing hybrid does *not* help, or helps only in a specific category, is a valid and
reportable outcome.

## Dataset
- **Knowledge base**: `docs.jsonl` — 60 original, synthetic short documents about Nginx
  administration (directives, error codes, common troubleshooting topics). Text is
  written from scratch, not copied from official Nginx documentation.
- **Test set**: `qa_final_180.jsonl` — 180 questions, 60 per category:
  - `keyword`: question deliberately reuses the target document's core technical
    identifier (a specific directive name or HTTP status code).
  - `semantic`: paraphrased description of the same underlying problem/concept,
    deliberately avoiding that specific identifier (may still share generic domain
    vocabulary — e.g. "connections", "log", "cache" — since natural language about the
    same topic inevitably shares some words; only the *specific distinguishing term* is
    withheld).
  - `troubleshooting`: a realistic multi-symptom scenario. These are intentionally
    harder and may share surface vocabulary with a neighboring, related document,
    so retrieval is not trivially one-to-one.
  - Every question includes: `id`, `question`, `category`, `expected_doc_id`,
    `reference_answer` (a short, paraphrased answer, not copied from the doc text).
- **Label verification**: every `keyword` question was programmatically checked to
  contain its target document's core term, and every `semantic` question was checked to
  *not* contain that exact term. Two initial mismatches were found this way and fixed
  before finalizing the dataset. This check is in `build_qa_180.py`.
- **No train/test split / no leakage**: this is a retrieval evaluation, not a trained
  classifier; there is nothing to leak between "train" and "test" since no model is
  fit on this data — the embedding model is used off-the-shelf.

## Retrieval Methods
1. **Dense only**: `all-MiniLM-L6-v2` sentence embeddings, FAISS `IndexFlatL2` exact
   search (no approximation).
2. **BM25 only**: `rank_bm25`, whitespace-tokenized, lowercased.
3. **Hybrid**: for each query, min-max normalize the dense similarity scores and the
   BM25 scores independently across the corpus, then combine:

   ```
   hybrid_score = alpha * normalized_dense_similarity + (1 - alpha) * normalized_BM25_score
   ```

   Swept over alpha in {0.00, 0.25, 0.50, 0.75, 1.00}. alpha=1.00 reduces to dense-only;
   alpha=0.00 reduces to BM25-only — both are reported in the sweep as a sanity check
   against the standalone methods.

## Evaluation Metrics
- **Recall@1, Recall@3, Recall@5**: was the correct `expected_doc_id` anywhere in the
  top-k retrieved list.
- **MRR**: computed from a single full top-5 ranked list per query (`1/rank` if the
  correct doc is present in the top 5, else 0), independent of which Recall@k is being
  reported alongside it. MRR is **not** derived from or forced to equal Recall@1 — the
  notebook computes it from the actual rank position.
- All metrics reported overall and broken down by category (keyword / semantic /
  troubleshooting).

## Experimental Procedure
1. Load and validate `docs.jsonl` and `qa_final_180.jsonl` (unique doc IDs, every
   `expected_doc_id` exists, category counts as designed).
2. Build dense (FAISS) and BM25 indexes over the 60 documents.
3. Evaluate dense-only and BM25-only on all 180 questions.
4. Evaluate hybrid at all 5 alpha values — the complete sweep is reported, not just the
   best-looking point.
5. Break down every configuration's results by category.
6. Select the single best-performing configuration by Recall@1 (MRR as tiebreak),
   chosen only *after* seeing the full table, and produce an error-analysis table of
   its Recall@1 and Recall@3 failures (question, category, expected doc, retrieved
   docs and their topics).
7. Export `final_results.json`, `category_breakdown.csv`, `error_analysis.csv`, and
   `alpha_sweep.csv` from Colab.

## Limitations
- The knowledge base is synthetic and single-domain (Nginx); results may not
  generalize to other technical domains or to real, noisier documentation.
- 60 documents / 180 questions is a small-scale evaluation appropriate for a short
  paper or workshop submission, not a large benchmark.
- `expected_doc_id` is a single ground-truth document per question; a few
  `troubleshooting` questions could plausibly be answered by more than one document,
  which is by design (realistic difficulty) but means Recall@k here is a slightly
  conservative estimate of "was a *useful* document retrieved" versus "was the *one*
  labeled document retrieved."
- Dense embeddings use one general-purpose model (`all-MiniLM-L6-v2`); results may
  differ with a larger or domain-adapted embedding model.

## What Must Come From Actual Colab Execution
Every number in the Results section of the paper — Recall@1/3/5, MRR, the full alpha
sweep table, the category breakdown table, and the error-analysis table — must come
from actually running `RAG_Final_Colab.ipynb` in Google Colab. Nothing in this
protocol document specifies or implies what those numbers will be.
