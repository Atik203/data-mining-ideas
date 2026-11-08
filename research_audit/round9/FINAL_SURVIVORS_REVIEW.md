# Final Review — Surviving Ideas and Pilot Evidence

**Date:** 8 November 2026  
**Scope:** surviving ideas only; killed ideas are intentionally omitted. Pilot figures were checked against the local ledgers/results.  
**Scoring:** Round-8 eight-factor rubric: Novelty 25%, Method 20%, Math 15%, Experiment 10%, Dataset 10%, Q1 fit 10%, Feasibility 5%, Compute 5%. **Core** = mean of first four; **Weighted** = weighted sum of all eight. Independence is separate. Scores below are conservative, evidence-adjusted working estimates—not publication probabilities.

## Current ranking

Ranked by Core; weighted score breaks ties.

| Rank | Idea | Nov | Method | Math | Exp | Data | Q1 | Feas | Compute | Core | Weighted | Indep. | Status |
|---:|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---|
| 1 | **Tool-Observation Sensitivity in Language-Model Agents: A Fixed-Prefix Causal Study (E2)** | 8.0 | 7.5 | 7.5 | 6.5 | 7.0 | 7.0 | 8.5 | 9.0 | **7.38** | **7.55** | 7 | Gate C: GO staged; 800 real prefixes, 3 models, $70 cap |
| 2 | **The Ranking Cost of Instruction Tuning: Quality Beyond Accuracy and Calibration (RANKCAL)** | 7.5 | 7.5 | 6.5 | 5.5 | 7.0 | 7.0 | 8.5 | 9.0 | **6.75** | **7.18** | 8.5 | Directional; N=2,500/TOST next |
| 3 | **Causal Retrieval Lift: Separating Retrieved Evidence from Answer Presence (R1)** | 6.8 | 7.0 | 6.0 | 6.8 | 6.0 | 7.0 | 8.0 | 9.0 | **6.65** | **6.83** | 8.5 | Gate B: conditional measurement paper; claims cut |
| 4 | **Constraint Age or Constraint Load? Separating Two Causes of Multi-Turn Instruction Decay (E1)** | 7.5 | 7.0 | 6.5 | 5.5 | 7.0 | 7.0 | 8.5 | 9.5 | **6.63** | **7.10** | 9 | Gate D: valid 3-arm factorial; new pilot needed |
| 5 | **The Causal Effect of Tool Use on Answer Accuracy: A Randomized Encouragement Study (Tool-IV)** | 7.5 | 7.0 | 6.5 | 5.5 | 6.5 | 6.5 | 8.5 | 9.0 | **6.63** | **6.98** | 8.5 | Conditional; corrected bounds, hard tasks |
| 6 | **Bangla Across Scripts: Native-to-Romanized Transfer and Contrastive Alignment (M1)** | 7.0 | 7.0 | 5.0 | 5.0 | 6.0 | 7.0 | 7.5 | 8.5 | **6.00** | **6.50** | 8 | Gate F: n inflated; direction only; 9k/7B + native audit |
| 7 | **Watermark Survival Under Paraphrase: Surface Change, Not Semantic Distance (T1)** | 5.5 | 6.5 | 4.5 | 6.0 | 7.0 | 7.0 | 7.5 | 9.0 | **5.63** | **6.18** | 10 | **Gate A: mean-radius ρ_c killed; Wang 2026 preempts threshold; only non-identifiability + p̄ mechanism survives** |

Arithmetic examples: T1 Core=(8+8+7+7.5)/4=7.625; Weighted=.25(8)+.20(8)+.15(7)+.10(7.5)+.10(7)+.10(8)+.05(7.5)+.05(9)=7.725. E1 Core=(7.5+7+6.5+5.5)/4=6.625; Weighted=7.10. These are deliberately below the optimistic Round-8 scan scores.

## Pilot verification and interpretation

### T1 watermark — white-box plant passed; phase-boundary theory not yet tested

Fresh run of `pilot_idea1_wb/wb_analyze.py` reproduced the saved results on 100 paired prompts: Δ green fraction=.144 at δ=2, AUC=.9886, clean/watermarked/weak/strong/control detector rates .06/.92/.88/.07/.83, strong-vs-watermarked error difference .85 (McNemar b=86,c=1,p≈1.1e−24). Weak paraphrase was close to watermarked text by MiniLM distance (.009); strong was farther (.187). The token-overlap synonym control retained much of the signal (83% detection), so it is a limited control. This confirms the planted-watermark/paraphrase mechanism at 0.5B, **not** a minimax phase transition, a fitted critical radius, or generalization across models.

**Gate A correction (8 Nov):** the mean-radius theorem is **false as formulated**. A mixture attack gives \(E^*(\rho)\ge\min(1,\rho/\rho_0)\) — a linear floor, not a sharp threshold. The missing lemma \(\eta=\eta(\rho)\) fails (semantic radius does not identify green-status retention), the \(\chi^2\) key-average proof is invalid for the shared additive key, and the threshold formula is negative at the pilot's \(n=200\). What survives is a **fixed-kernel retained-signal boundary**: \(r=1-\bar p\), with \(\eta_c\approx0.39\) and burstiness \(n_{\text{eff}}=n/\tau^2\), fitting 4/5 pilot arms. Wang et al. (NeurIPS 2026) already proves the \(\Delta\rho\) retained-signal threshold, so the empirical threshold law is preempted. The remaining contribution is a **non-identifiability + surface-change mechanism** measurement, not an 8+ theorem. Revised score: **Core 5.63 / Weighted 6.18**.

### E1 turn-decay — initial PASS overstates what was identified

Raw CSV aggregation reproduced t1/t2/t3 rates exactly: neutral .86/.75/.71; strict .88/.78/.733. Original model (lag + constraint-type FE) gives λ=.461/.481 with nominal LRT p=.0027/.0006. However, every turn adds another active constraint. I independently refit lag with **turn fixed effects** and dialogue-clustered SEs:

| Style | Turn-adjusted λ | Clustered 95% CI | LRT p |
|---|---:|---:|---:|
| Neutral | .257 | [−.146,.660] | .145 |
| Strict | .333 | [−.059,.725] | .037 |

Exact-constraint FE also leaves both clustered CIs crossing zero. Only one model was tested (the “second model” was a stricter prompt), 3 turns cannot validate a 3-parameter exponential law, and 24/22 of 40 constraints were excluded for ceiling/floor. **Current result is confounded/marginal, not confirmation of a portable retention λ.** Redesign a factorial separating constraint age from number of simultaneous constraints; then test on multiple models and held-out tasks.

**Gate D redesign (8 Nov):** valid 3-arm × 3-family × 60-dialogue × 8-turn design. **PS:** one persistent constraint, load 1, ages 0–7. **FC:** \(k_t\in\{1..5\}\) fresh constraints/turn, age 0. **RR:** one persistent anchor + \(k_t-1\) fresh fillers, anchor entry randomized independently of load (age⊥load, \(r=-.03\)). Estimand: GEE logistic with turn/arm/family FE; \(\beta_a\)=retention decay at fixed load, \(\beta_k\)=load cost at fixed age, \(\Delta=\beta_a-\beta_k\). MDE at 60 dialogues/arm/family: \(\beta_a=.076\), \(\beta_k=.060\), interaction .039, \(\Delta=.084\) (power .84 at .10). Test: two 1-df LRTs in M0 + 1-df Wald for \(\Delta\) + interaction LRT. Go only if randomization checks pass and both slopes reach SESOI .10; if only load moves, it is a load-cost paper; if only age moves, retention is confirmed. Cost ≈$10–25, 4–6h.

### R1 retrieval lift — primary effect strong; contamination/twin extension fails

Independently aggregated the 700-row ledger (200 base queries × 3 arms plus 100 twin rows): top-1 gold=.400, shuffled=.000, empty=.075; paired τ=.325 and δ=−.075, matching the saved analysis. Answer was present in 109/200; exact match was 80/109 when present and 0/91 when absent. The v2 twin rows recompute to 37/50 containing the swapped answer (74%) and 26/50 strict EM (52%), below the preregistered 90% validity gate. The empty arm is **not** a contamination estimate by itself; it also includes reasoning, guessing, and task priors. Keep only the randomized **retrieval-lift/passage-reliance** claim. Do not claim contamination bounds or conflict robustness without valid twins.

### E2 tool-observation sensitivity — small screening signal

Independently recounted N=60: consult 54/60=.90, placebo jitter 0/60, error-vs-reference action switch 5/60=.0833; exact McNemar p=.0625, CI [.013,.153]. The free-text first attempt was correctly discarded because its baseline changed the response format; the fact-matched categorical comparison is the valid pilot. This is a **promising screening signal**, not a confirmed dose-response law. Scale to hundreds of real prefixes and multiple severity levels.

**Gate C scale-up design (8 Nov):** GO, staged, hard cap $70. 800 real τ-bench prefixes (400 airline, 400 retail), 3 open-weight 7–8B models, 5 observation arms (verbatim JSON anchor + s=0…3 prose), 4-step recovery. MDE 0.20 logit/level per model at N=800 (OR 1.22); pooled ≤0.15; the pilot-implied b≈0.8 gives >99% power. Recovery t½ CI ±16–20% per model, ±9–11% pooled; model/severity ranking needs 32–50% and is out of scope. Harvest supply is measured: 6,554 eligible value-used prefixes across 165 task–domain pairs. Cost $63 base/$70 cap. The claim is a bounded content-sensitivity and recovery result, not a universal law.

### RANKCAL — apparent ranking shift not significant at pilot size

Independent recomputation on 500 raw rows: accuracy .512→.544, AUROC .7652→.7259, ECE .1333→.1818. DeLong ΔAUROC=−.0393, 95% CI [−.0821,.0034], MDE≈.061; ΔAURC CI includes zero. The ECE rise is significant and isotonic closes 84% of it. No subject bucket met n≥15. Conclusion: directional ranking signal only; no confirmed rank-tax claim. Needs N≥1500 and preregistered coarse groups.

### Tool-IV — weak outcome contrast despite a healthy first stage

Independent raw counts: Z=0: D=0, Y=.960; Z=1: D=.267, Y=.987; ITT=.027, γ=.267, F=26.9, Wald LATE=.10, CI [−.098,.298]. Baseline accuracy is at ceiling. One-sided bounds have width 1.15; the Balke–Pearl implementation returned inverted limits and was correctly discarded. The nudge’s exclusion restriction remains doubtful. This is marginal; redesign with harder tasks, stronger first stage, placebo nudge, and correct sharp-bound code before spending on cloud.

### M1 script transfer — positive direction, not statistically confirmed

Fresh run of the saved analyzer on `results_i04.csv`: baseline native=.518, roman=.400 (gap=11.82pp, paired t p=.052, McNemar p=.074); InfoNCE arm roman=.464 (gain=6.36pp, p=.127). This clears the pilot’s **point-estimate** gate but not conventional significance. The 30/30 check was AI scheme-conformance, not native-speaker validation. Treat as a directional pilot; get native review and larger N before claiming an effect. The proposed η minimax bound remains unproved.

**Gate F correction (8 Nov):** the 110 “cells” are 89 unique items in 7 source groups, and the two seeds test different groups (only 21/89 items overlap; seed 42 gap=19.57pp, seed 1234 gap=6.25pp). Cluster-bootstrap 95% CI is **[0.35, 25.06]pp**, honest MDE80 ≈ **22–32pp**. InfoNCE used batch 2, so it had no in-batch negatives. The pilot therefore establishes **direction only**, not magnitude. The η theorem is dropped from the claim set: it is untestable in this design and its “matching” lower bound is a Bayes-floor restatement. Proceed only as an empirical transfer paper with 9k items, a frozen document-clustered split, 7B QLoRA, a shuffled-pair placebo, and a native-speaker audit (~$320) before GPU spend. Revised working score: **Core 6.0 / Weighted 6.5**.

## Recommendation

1. **No surviving idea is currently a verified 8+ paper.** T1’s mean-radius theorem is now **killed**; its surviving non-identifiability/\(\bar p\) mechanism is a ~5.6/6.2 measurement note, not the lead.
2. **Best current paper: E2 (7.38/7.55)** — Gate C passes as a staged GO: 800 real prefixes, 3 open-weight 7–8B models, 5 arms, MDE 0.20 logit/level, $63 base/$70 cap.
3. **Next: RANKCAL (6.75/7.18)** with N=2,500 and TOST, and **R1 (6.65/6.83)** scoped to randomized retrieval lift. R1’s contamination/twin/conflict claims are removed.
4. **Tool-IV (6.63/6.98)** is conditional: hard tasks, corrected sharp bounds, and a placebo-nudge exclusion check before any GPU spend.
5. **E1 (6.63/7.10): run the new 3-arm factorial** (PS/FC/RR) before scaling; Gate D’s MDEs are β_a=.076, β_k=.060, Δ=.084.
6. **T1 (5.63/6.18):** keep only a bounded negative-result/measurement note on non-identifiability and surface-change rate; do **not** run the old 7B phase-boundary experiment.
7. **Do not rent GPU yet.** Run E2 G0 (CPU harvest/freeze) and G1 (3 GPU-hours calibration) first; rent only if G0/G1 pass. If E2 fails, run RANKCAL (2,500 items, TOST).

Verification artifacts: `verify_e1_load_confounds.py` + `.json`; `pilot_idea1_whitebox.md`; `pilot_idea8_realcorpus.md`; `pilot_idea5.md`; `pilot_idea7.md`; `pilot_idea10.md`; `pilot_idea4.md`.
