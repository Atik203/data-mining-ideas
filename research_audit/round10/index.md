# Round 10 — Final Main-Track Ranking

**Date:** 8 November 2026  
**Status:** all six main-track gates complete. The green-computing track is separate: `https://github.com/Atik203/green-computing-ideas`.  
**Scope:** surviving main-track ideas only. Killed ideas are intentionally omitted.  
**Scoring:** Round-8 eight-factor rubric — Novelty 25%, Method 20%, Math 15%, Experiments 10%, Dataset 10%, Q1 fit 10%, Feasibility 5%, Compute 5%. **Core** = mean of the first four; **Weighted** = all eight. Independence is reported separately. Scores are conservative, evidence-adjusted working estimates, not acceptance probabilities.

---

## Bottom line

**Best executable main-track paper: E2 — fixed-prefix tool-observation sensitivity (Core 7.38 / Weighted 7.55).** It has a real causal estimand, a screening pilot, a harvested supply of 6,554 eligible real prefixes, a 0.20-logit MDE at N=800, and a hard cash cap of $70. It is a **7.4 paper with an 8+ path**, not a verified 8+ paper.

**The theory lead is gone.** T1's mean-semantic-radius phase-transition theorem is false as formulated; the surviving surface-change mechanism is pre-empted by Wang et al. (NeurIPS 2026) and is now a **5.63 / 6.18** measurement note. Do not run the old 7B phase-boundary experiment.

**No survivor is a verified 8+ paper.** The honest main-track board is E2 7.38, RANKCAL 6.75, R1 6.65, E1 6.63, Tool-IV 6.63, M1 6.00, T1 5.63. A 7+ paper is a good outcome; the team should execute the best 7+ paper and stop chasing the label.

---

## Final ranking

| Rank | ID | Title | Nov | Method | Math | Exp | Data | Q1 | Feas | Compute | Core | Weighted | Indep. | Gate verdict |
|---:|---|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---|
| 1 | E2 | **Tool-Observation Sensitivity in Language-Model Agents: A Fixed-Prefix Causal Study** | 8.0 | 7.5 | 7.5 | 6.5 | 7.0 | 7.0 | 8.5 | 9.0 | **7.38** | **7.55** | 7.0 | **GO, staged** — 800 real prefixes, 3 models, 5 arms; $63 base / $70 cap |
| 2 | RANKCAL | **The Ranking Cost of Instruction Tuning: Quality Beyond Accuracy and Calibration** | 7.5 | 7.5 | 6.5 | 5.5 | 7.0 | 7.0 | 8.5 | 9.0 | **6.75** | **7.18** | 8.5 | **GO, conditional** — 2,500 MMLU items; paired DeLong + TOST ±0.03 |
| 3 | R1 | **Causal Retrieval Lift: Separating Retrieved Evidence from Answer Presence** | 6.8 | 7.0 | 6.0 | 6.8 | 6.0 | 7.0 | 8.0 | 9.0 | **6.65** | **6.83** | 8.5 | **GO, conditional** — bounded measurement paper; N=800; invalid claims cut |
| 4 | E1 | **Constraint Age or Constraint Load? Separating Two Causes of Multi-Turn Instruction Decay** | 7.5 | 7.0 | 6.5 | 5.5 | 7.0 | 7.0 | 8.5 | 9.5 | **6.63** | **7.10** | 9.0 | **REDESIGN DONE** — run the new 3-arm factorial; current pilot is confounded |
| 5 | Tool-IV | **The Causal Effect of Tool Use on Answer Accuracy: A Randomized Encouragement Study** | 7.5 | 7.0 | 6.5 | 5.5 | 6.5 | 6.5 | 8.5 | 9.0 | **6.63** | **6.98** | 8.5 | **GO, conditional** — calibration then 4,500 calls; corrected BP bounds |
| 6 | M1 | **Bangla Across Scripts: Native-to-Romanized Transfer and Contrastive Alignment** | 7.0 | 7.0 | 5.0 | 5.0 | 6.0 | 7.0 | 7.5 | 8.5 | **6.00** | **6.50** | 8.0 | **CONDITIONAL** — empirical only; 9k/7B + ~$320 native audit |
| 7 | T1 | **Watermark Survival Under Paraphrase: Surface Change, Not Semantic Distance** | 5.5 | 6.5 | 4.5 | 6.0 | 7.0 | 7.0 | 7.5 | 9.0 | **5.63** | **6.18** | 10.0 | **STOP theory** — mean-radius theorem killed; measurement note only |

Arithmetic examples: E2 Core = (8.0+7.5+7.5+6.5)/4 = 7.375 → 7.38. R1 Core = (6.8+7.0+6.0+6.8)/4 = 6.65. T1 Core = (5.5+6.5+4.5+6.0)/4 = 5.625 → 5.63.

---

## What each gate changed

| Gate | ID | Title | Verdict | What changed | Revised score |
|---|---|---|---|---|---|
| **A** | T1 | Watermark Survival Under Paraphrase: Surface Change, Not Semantic Distance | **WEAKEN / STOP theory** | Mean-radius $\rho_c(\delta,n)$ is killed by a mixture attack; the missing $\eta=\eta(\rho)$ lemma is false; the $\chi^2$ proof is invalid; the threshold is negative at $n=200$; Wang et al. (NeurIPS 2026) pre-empts the retained-signal threshold. Only the $\bar p$ surface-change mechanism survives (fits 4/5 pilot arms, $\eta_c\approx0.39$). | **5.63 / 6.18** |
| **B** | R1 | Causal Retrieval Lift: Separating Retrieved Evidence from Answer Presence | **CONDITIONAL GO** | Keep only the randomized retrieval-lift estimand, answer-presence decomposition, and random-passage penalty. Drop contamination, twin/conflict, Manski, and minimax claims. The 74% twin result is model compliance, not construction validity. N=800 gives 5.5pp MDE for $\tau$. | **6.65 / 6.83** |
| **C** | E2 | Tool-Observation Sensitivity in Language-Model Agents: A Fixed-Prefix Causal Study | **GO, staged** | 800 real $\tau$-bench prefixes (400 airline / 400 retail), 3 open-weight 7–8B models, 5 observation arms, 4-step recovery. MDE 0.20 logit/level per model; pooled ≤0.15; pilot-implied $b\approx0.8$. 6,554 eligible prefixes across 165 task–domain pairs. $63 base / $70 hard cap. | **7.38 / 7.55** *(stands until data)* |
| **D** | E1 | Constraint Age or Constraint Load? Separating Two Causes of Multi-Turn Instruction Decay | **REDESIGN DONE** | The pilot confounds constraint age with active load: turn FE cuts $\lambda$ from .461/.481 to .257/.333, and both clustered CIs cross zero. New 3-arm × 3-family × 60-dialogue × 8-turn factorial: PS (age only), FC (load only), RR (age⊥load, $r=-.03$). MDE $\beta_a=.076$, $\beta_k=.060$, $\Delta=.084$. | **6.63 / 7.10** |
| **E** | RANKCAL | The Ranking Cost of Instruction Tuning: Quality Beyond Accuracy and Calibration | **GO, conditional** | 2,500 MMLU items; Qwen2.5-7B base/Instruct + Llama-3.1-8B replication; four pre-registered MMLU groups; paired DeLong + TOST ±0.03. MDE 0.028 item-level, 0.036 cluster-robust. Equivalence is publishable as a null paper. | **6.75 / 7.18** |
| **E** | Tool-IV | The Causal Effect of Tool Use on Answer Accuracy: A Randomized Encouragement Study | **GO, conditional** | Calibration (400 items, $p_0=0.60$–$0.70$, $\gamma\ge0.35$), then 4,500 calls on hard tasks; 3 arms C/P/T; correct BP LP. MDE LATE 0.089 at $\gamma=.50$. Stop if $\gamma<.35$, placebo $|\delta|>.06$, or LATE CI includes 0 with upper $<.10$. | **6.63 / 6.98** |
| **F** | M1 | Bangla Across Scripts: Native-to-Romanized Transfer and Contrastive Alignment | **CONDITIONAL / empirical only** | The $\eta$ theorem is dropped. The pilot's 110 "cells" are 89 unique items in 7 groups; seeds test different groups (21/89 overlap); cluster CI [0.35, 25.06]pp; MDE 22–32pp. Proceed only with 9k items, frozen document-clustered split, 7B QLoRA, shuffled-pair placebo, and a ~$320 native-speaker audit. | **6.00 / 6.50** |

---

## Decision

### Recommendation: Tool-Observation Sensitivity in Language-Model Agents (E2)

Run **Tool-Observation Sensitivity in Language-Model Agents** (E2) as the main paper. It is the only survivor with all five properties at once: a clean causal estimand, a real pilot signal, an empirically harvested prefix supply, a powered design, and a budget inside the cap. The claim is deliberately bounded: the graded effect of observation content on the next decision for open-weight 7–8B models, plus a recovery half-life. It is **not** a universal law and **not** a model-ranking paper.

**Sequence:**

1. **G0 (CPU, ~2 days, $0):** harvest 880 eligible prefixes, audit 40, replay-verify ≥98%, freeze 800. The supply is already measured: 6,554 eligible candidates, min 7 / median 38 per task.
2. **G1 (GPU, ~3 h, ~$2):** 60-prefix calibration per model. Pass: parse ≥95%, placebo jitter ≤20%, s=3 switch 10–80%.
3. **Main run (35–45 GPU-h, $15–22):** 34,800 generations on a 24GB rental; stop at $22 or 45 GPU-h.
4. **Labels + analysis (~1 week):** 160-item double-coding, κ≥.80; conditional-logit slope; recovery half-life.

**Cash:** $63 base, **$70 hard cap**. No paid API. If only a $0.89/h secure 4090 is available, cap at 30 GPU-h and reduce N to 600.

### Fallback: The Ranking Cost of Instruction Tuning (RANKCAL)

If E2's G0/G1 fails (prefix supply, construct validity, or calibration ceiling), run **The Ranking Cost of Instruction Tuning** (RANKCAL). It is cheaper and cleaner: 2,500 MMLU items on 7B base/Instruct, paired DeLong, TOST ±0.03. It answers a precise question — does instruction tuning degrade ranking quality even when accuracy rises? — and both a positive and an equivalence result are publishable. Do not run RANKCAL and E2 at the same time; one paper, one budget.

### Do not start

- **Watermark Survival Under Paraphrase (T1).** The theorem is dead and the empirical threshold is pre-empted.
- **Causal Retrieval Lift (R1)** contamination/twin/conflict extensions. The pilot does not identify them; they are separate studies.
- **Constraint Age or Constraint Load? (E1)** at scale before the new pilot. The old design cannot separate age from load.
- **Bangla Across Scripts (M1)** before the native audit. Without it, the corpus validity claim fails and the GPU spend is wasted.

---

## Budget and sequencing

| Step | Track | Cash | GPU | Gate |
|---|---|---:|---:|---|
| E2 G0 harvest/freeze | main | $0 | 0 | 880 eligible, audit ≥90%, replay ≥98% |
| E2 G1 calibration | main | ~$2 | 3 h | parse ≥95%, jitter ≤20%, s=3 10–80% |
| E2 main run | main | $15–22 | 35–45 h | slope replicated ≥2/3 models |
| RANKCAL | fallback | ~$5–10 | 8–12 h | TOST ±0.03; group guardrail |
| R1 | fallback | ~$2–5 | 3–5 h | τ ≥5pp at N=800 |
| E1 new pilot | later | $10–25 | 4–6 h | both slopes reach SESOI .10 |
| Tool-IV | later | ~$10–15 | 6–8 h | γ≥.35, placebo |δ|≤.06 |
| M1 | later | ~$340–355 | 52–76 h | native audit passes |

**Rule:** rent the 24GB GPU only after G0/G1 (E2) or the calibration stage (RANKCAL/Tool-IV) passes. The local 3060 is for pilots and green experiments, not for the main 7B run.

---

## Plain-language idea pages

- `ideas/watermark-survival-under-paraphrase.md` — Watermark Survival Under Paraphrase (T1)
- `ideas/tool-observation-sensitivity.md` — Tool-Observation Sensitivity in Language-Model Agents (E2)
- `ideas/causal-retrieval-lift.md` — Causal Retrieval Lift (R1)
- `ideas/ranking-cost-of-instruction-tuning.md` — The Ranking Cost of Instruction Tuning (RANKCAL)
- `ideas/constraint-age-or-load.md` — Constraint Age or Constraint Load? (E1)
- `ideas/causal-effect-of-tool-use.md` — The Causal Effect of Tool Use on Answer Accuracy (Tool-IV)
- `ideas/bangla-script-transfer.md` — Bangla Across Scripts (M1)

Each page uses the same sections: **What / Why / How / Dataset / Method / Expected / Results / Outcome**.

## Green track

The green-computing shortlist, ranking, and post-pilot recommendation are in `https://github.com/Atik203/green-computing-ideas`. **Certified Carbon-Aware Quantization and Sparsity Allocation (G3) was killed by its pilot** (NF4 energy loss, no feasible mix at \(0.8\times\)FP16, additive \(R^2\) fail). The provisional green lead is now **Certified Energy-Normalized Data Selection for Instruction Tuning (G4, 7.50/7.78)**, pending its pilot; **Energy-Optimal Retrieval Depth (G2, 7.00/7.35)** and **Carbon per Correct Answer (G5, 7.00/7.03)** are the 7.0 fallbacks.

## Evidence

- Gate reports: `01_t1_theorem_gate.md`, `02_r1_scoped_paper.md`, `03_e2_scaleup.md`, `04_e1_redesign.md`, `05_rankcal_tooliv_fixes.md`, `06_m1_scaleup.md`
- Survivor review and independent pilot checks: `../round9/FINAL_SURVIVORS_REVIEW.md`
- Running scores and corrections: `../../HIGH_SCORE_IDEAS_MEMORY.md`
- Live task list: `../../TODO.md`
