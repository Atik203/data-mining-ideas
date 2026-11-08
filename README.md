# Data-Mining Research Ideas — 2026

**Course track:** data mining / LLM evaluation and measurement  
**Team:** 3–4 people · 6–7 months · local RTX 3060 6 GB · 24 GB rental available · public data · ≤ $70 cash  
**Target:** a good Q1 paper. A 7+ paper is a strong outcome; 8+ only if genuinely verified.

This repository is the **data-mining track** of a two-track selection package. The green-computing track is separate: **https://github.com/Atik203/green-computing-ideas**.

---

## Start here

| If you want… | Read |
|---|---|
| The current ranking and recommendation | [`research_audit/round10/index.md`](research_audit/round10/index.md) |
| One page per surviving idea | [`research_audit/round10/ideas/`](research_audit/round10/ideas/) |
| The verified pilot evidence and corrections | [`research_audit/round9/FINAL_SURVIVORS_REVIEW.md`](research_audit/round9/FINAL_SURVIVORS_REVIEW.md) |
| The six gate reports | [`research_audit/round10/`](research_audit/round10/) |
| The running verdict tracker | [`research_audit/TRACK.md`](research_audit/TRACK.md) |

---

## Current ranking (8 November 2026)

| Rank | ID | Title | Nov | Method | Math | Exp | Data | Q1 | Feas | Compute | Core | Weighted | Indep. | Decision |
|---:|---|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---|
| 1 | E2 | **Tool-Observation Sensitivity in Language-Model Agents: A Fixed-Prefix Causal Study** | 8.0 | 7.5 | 7.5 | 6.5 | 7.0 | 7.0 | 8.5 | 9.0 | **7.38** | **7.55** | 7.0 | **GO, staged** — 800 real τ-bench prefixes, 3×7–8B, 5 arms, $63/$70 |
| 2 | RANKCAL | **The Ranking Cost of Instruction Tuning: Quality Beyond Accuracy and Calibration** | 7.5 | 7.5 | 6.5 | 5.5 | 7.0 | 7.0 | 8.5 | 9.0 | **6.75** | **7.18** | 8.5 | **GO, conditional** — 2,500 MMLU + TOST ±.03 |
| 3 | R1 | **Causal Retrieval Lift: Separating Retrieved Evidence from Answer Presence** | 6.8 | 7.0 | 6.0 | 6.8 | 6.0 | 7.0 | 8.0 | 9.0 | **6.65** | **6.83** | 8.5 | **GO, conditional** — N=800 measurement paper |
| 4 | E1 | **Constraint Age or Constraint Load? Separating Two Causes of Multi-Turn Instruction Decay** | 7.5 | 7.0 | 6.5 | 5.5 | 7.0 | 7.0 | 8.5 | 9.5 | **6.63** | **7.10** | 9.0 | **Redesign done** — run the new 3-arm factorial |
| 5 | Tool-IV | **The Causal Effect of Tool Use on Answer Accuracy: A Randomized Encouragement Study** | 7.5 | 7.0 | 6.5 | 5.5 | 6.5 | 6.5 | 8.5 | 9.0 | **6.63** | **6.98** | 8.5 | **GO, conditional** — calibration then 4,500 calls |
| 6 | M1 | **Bangla Across Scripts: Native-to-Romanized Transfer and Contrastive Alignment** | 7.0 | 7.0 | 5.0 | 5.0 | 6.0 | 7.0 | 7.5 | 8.5 | **6.00** | **6.50** | 8.0 | **Conditional** — native audit, then 9k/7B |
| 7 | T1 | **Watermark Survival Under Paraphrase: Surface Change, Not Semantic Distance** | 5.5 | 6.5 | 4.5 | 6.0 | 7.0 | 7.0 | 7.5 | 9.0 | **5.63** | **6.18** | 10.0 | **Stop theory** — measurement note only |

**Recommendation:** execute **E2**; fallback **RANKCAL**. **No verified 8+ candidate exists.** T1’s mean-radius theorem was killed in Gate A, and Wang et al. (NeurIPS 2026) pre-empts the retained-signal threshold.

Scores use the Round-8 eight-factor rubric: Novelty 25%, Method 20%, Math 15%, Experiments 10%, Dataset 10%, Q1 fit 10%, Feasibility 5%, Compute 5%. Core = mean of the first four; Weighted = all eight. They are conservative working estimates, **not acceptance probabilities**.

---

## Idea pages

Each page has the same sections: **What / Why / How / Dataset / Method / Expected / Results / Outcome**.

| Idea | Page |
|---|---|
| Tool-Observation Sensitivity in Language-Model Agents: A Fixed-Prefix Causal Study (E2) | [`tool-observation-sensitivity.md`](research_audit/round10/ideas/tool-observation-sensitivity.md) |
| The Ranking Cost of Instruction Tuning: Quality Beyond Accuracy and Calibration (RANKCAL) | [`ranking-cost-of-instruction-tuning.md`](research_audit/round10/ideas/ranking-cost-of-instruction-tuning.md) |
| Causal Retrieval Lift: Separating Retrieved Evidence from Answer Presence (R1) | [`causal-retrieval-lift.md`](research_audit/round10/ideas/causal-retrieval-lift.md) |
| Constraint Age or Constraint Load? Separating Two Causes of Multi-Turn Instruction Decay (E1) | [`constraint-age-or-load.md`](research_audit/round10/ideas/constraint-age-or-load.md) |
| The Causal Effect of Tool Use on Answer Accuracy: A Randomized Encouragement Study (Tool-IV) | [`causal-effect-of-tool-use.md`](research_audit/round10/ideas/causal-effect-of-tool-use.md) |
| Bangla Across Scripts: Native-to-Romanized Transfer and Contrastive Alignment (M1) | [`bangla-script-transfer.md`](research_audit/round10/ideas/bangla-script-transfer.md) |
| Watermark Survival Under Paraphrase: Surface Change, Not Semantic Distance (T1) | [`watermark-survival-under-paraphrase.md`](research_audit/round10/ideas/watermark-survival-under-paraphrase.md) |

---

## What is inside

```
research_audit/
├── round10/
│   ├── index.md                 ← ranking and recommendation
│   ├── 01…06 gate reports
│   └── ideas/                   ← seven plain-language pages
├── round9/
│   └── FINAL_SURVIVORS_REVIEW.md
├── TRACK.md
├── HIGH_SCORE_IDEAS_MEMORY.md
└── RESEARCH_SELECTION_PACKAGE.md
```

Each idea page uses **What / Why / How / Dataset / Method / Expected / Results / Outcome**. The “Outcome” line is the decision.

## What is not here

Raw pilot data, model weights, ledgers, the historical archive, and any credential. Some documents link to files in the full local workspace; those links may not resolve in this curated subset.

## Security

No API key, Hugging Face token, or `.env` file is included. The local workspace previously contained a hardcoded HF token in a pilot script; it has been redacted, and the token should be rotated separately.

## Status

Snapshot: **8 November 2026**. All six main-track gates are complete. Green pilots continue in the separate green repository.
