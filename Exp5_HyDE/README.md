# Experiment 5 — Learned Query Router (replaces Exp4's hand-crafted rules)

Motivation: Experiment 4's oracle analysis showed a large headroom (86.7% vs 76.1%
best fixed) that a hand-crafted rule-based router failed to capture (48.3%
classification accuracy). This experiment tests a learned classifier instead, using
proper 5-fold stratified cross-validation (every prediction is out-of-fold — no query
is ever routed by a model that saw it during training).

- `RAG_Exp5_Colab.ipynb` — self-contained. Upload `../data/docs.jsonl` and
  `../data/qa_final_180.jsonl` when prompted, run all cells top to bottom.
- Two router variants compared: category-classifier (predicts keyword/semantic/
  troubleshooting, then maps to a fixed method) vs. direct-method-classifier (predicts
  the oracle-best method directly, skipping the category proxy).
- Includes feature importance analysis and a cost-accuracy Pareto sweep (escalation
  threshold for when to use the expensive reranker).
- After running, four files download from Colab: `experiment5_results.json`,
  `experiment5_summary_table.csv`, `experiment5_feature_importance.csv`,
  `experiment5_pareto_sweep.csv` — save them here once you have them.
