# High-Score Ideas Memory — Agentic + LLM/ML/NLP

Date: 2026-11-07. Resources: stats/theory collaborator, 16GB VRAM possible, $70 max, extended time, new dataset allowed, 7B models ok. Avoid: debate, hallucination-risk, IntentGate-auth, FYDP-deliberation duplicates.

## Kept Idea 1 — Encouraged tool benefit [TOP PICK]
Randomized encouragement Z for tool use, IV LATE + sharp bounds when exclusion fails, 8-10k paired trajectory dataset. Scores: Nov 8.5, Method 8.0, Math 8.0, Exp 7.5, Data 8.0, Journal 7.5, Feas 8.0, Compute 8.5. Weighted 8.05, Core 8.03, Indep 8.5/10.

## Kept Idea 2 — Minimax audit allocation
B audits across T actions vs adaptive attacker, lower bound + matching policy, adaptive-attack trace dataset. Scores: Nov 8.0, Method 8.0, Math 8.5, Exp 6.5, Data 7.0, Journal 7.0, Feas 7.0, Compute 8.0. Weighted 7.73, Core 7.67.

## Kept Idea 3 — Drift-aware memory with regret bound
Reuse-vs-discard under tool drift, regret bound + drifted-tool benchmark. Scores: Nov 7.5, Method 7.5, Math 7.5, Exp 7.0, Data 7.5, Journal 7.0, Feas 7.5, Compute 8.0. Weighted 7.43, Core 7.33.

## Active search
Round-8: 4 background workstreams searching LLM reasoning/planning, multilingual/low-resource, calibration/alignment, RAG/evaluation for additional 7.5+ core ideas under same resources. Do not cap novelty. Each must give estimand/functional, theorem shape, dataset plan, initial 8-param scores, and 2-3 day kill test.

## Round-8 Lane 1 result (2026-11-07)
Top: A — Risk-controlled CoT early stopping. Weighted 8.375, Core 8.375, Indep 8/10. Estimand R(tau), C(tau), CRC threshold + cost-regret bound. Kill test: 300 GSM8K 7B traces, flips >=10%, AUC>0.6; CRC 200/100, kill if risk overshoot >5pp or savings <15%. PROVISIONAL — needs 2-3 day audit (see below).
Runners: B planning budget-allocation 7.85/7.875, C ICL portfolio 7.80/7.875. File: round8/01_llm_reasoning_scan.md.

## Round-8 Lane 2 result (2026-11-07)
Top: M1 SCRIPT-TRANSFER — script-factored transfer bound + paired triple alignment, Bangla NLU. Core 8.25, Weighted 8.19 (Nov 8.5, Met 8.0, Math 8.5, Exp 8.0). Lower bound Omega(eta+...) showing eta irreducible without paired data. Dataset 9-10k triples ~$30-50. Kill test: 500 NLI pairs, kill if gap <4pts or gain <1pt p>0.2.
Runners: M2 CS-PENALTY core 7.88, M3 BUDGET-ALLOC core 7.75. File: round8/02_multilingual_scan.md. Note: BanglaShield is safety-only, not transfer bound — must verify in audit.
Remaining: lanes 3 + 2 deep 8.5+ workstreams running.

## Round-8 Lane 4 result (2026-11-07)
Top: R1 ABLATE-RAG — causal retrieval-lift + contamination bounds, 3-arm ablation + twins. Core 8.00 / Weighted 8.00 (Nov 8, Meth 8, Math 8, Exp 8, Data 8.5). Bounds sharp Manski + Omega(1/pi^2) detection. Dataset FreshCounterfactualQA 8-10k. Budget $30-50, 120 GPU-h 7B. Kill test: 200Q x3 arms, kill if tau<2pp or shuffled>=gold or twin validity<90%. Closest descriptive only (2505.06914, 2605.14473, CDD 2603.03203).
Runners: R2 CERT-SELECT 7.67/7.70, R3 EFDR-RAG 7.42/7.53. File: round8/04_rag_eval_scan.md.

## Round-8 Lane 3 result (2026-11-07)
Top: R3-C TASKCAL-PROJ — confidence-subspace ablation of IT task vector. Core 7.83 / Weighted 7.86 (Nov 8.5, Meth 8.0, Math 8.0, Exp 7.0, Data 8.0). Scalar lambda dominated iff cos<1-gamma. Dataset TaskCal-Pairs-2k (direction only, no letter training — avoids StyleCal fail). Kill test: 7B tau + rank-1, kill if cos>0.9 or acc-2pp for ECE<0.02.
Runners: R3-A RANKCAL 7.75/7.86 (rank vs level, cheapest), R3-B GROUPCAL 7.75/7.92 (worst-group MCE). File: round8/03_calibration_scan.md. Note: Hu scalar-only; no confidence direction — must verify in audit.
Remaining: 1 deep 8.5+ (theory) workstream running.

## Round-8 Deep Empirical result (2026-11-07)
E1 TURN-DECAY law+dataset: S(t)=S0.exp(-λt)+c, portable λ_m, R2>0.5 target, 4-6k dialogues Latin-rotated. Core 8.50 / Weighted 8.48 (8.5x4), Indep 9/10. Closest Multi-IF (no law/rotation). Kill: 150 dialogues x2 models, kill if drop<6pp or R2<0.5.
E2 CTO-7B dataset+law: 1500 prefixes x4 arms, slope b_m + half-life. Core 8.50 / Weighted 8.55, Indep 7/10 (intensive vs Idea1 extensive). Kill: 120 prefixes, kill if consult<30% or gap<5pp or jitter>20%. Files: round8/06_deep_empirical_85.md.

## Round-8 Deep Theory result (2026-11-07)
T1 watermark phase transition: E*(rho,n) minimax sum-error, critical rho_c, achievability exp + impossibility chi2. Core 8.625 / Weighted 8.575 (Nov 9.0, Meth 8.5, Math 9.0, Exp 8.0), Indep 10/10. Dataset ParaMark-7B ~$20-40. Kill: 7B green-list Delta>0.05/AUC>0.6 Day1; strong paraphrase degrade >=15pp else drop.
T2 RAG k*-frontier: R*(k,n) joint retriever+reader, interior k*~(beta.n/logM). Core 8.50 / Weighted 8.375, Indep 8/10. Kill: NQ-200 spread<2pp or beta<=0.05 drop. File: round8/05_deep_theory_85.md. Note: Tavily quota blocked, IDs UNVERIFIED — recheck in kill tests.

## Round-9 audits (3060 small-scope) — partial
- Idea 3 CoT stopping: KILLED. Conformal Thinking ICML26 2602.03814 already claims CRC stopping. Core 6.00 / Wtd 6.00. File round9/03_idea3_cotstop.md.
- Idea 5 E2 CTO: KEEP pilot-gated. No fixed-prefix prior. Core 7.63 / Wtd 7.95, Indep 7/10. Kill: consult<30% or gap<5pp or jitter>20%.
## Pilot Idea 5 DONE: PASS — consult 90.0%, jitter 0%, gap 8.3pp [1.3,15.3] McNemar p=0.0625. V1 free-text failed honestly (extensive-margin confound), repaired fact-matched + categorical coding, 12/12 spot-check. True sensitivity exceeds 8.3pp. Files round9/pilot_idea5.md + ledger_P_I05.csv (60 rows).
- Idea 6 T2 RAG k*: MARGINAL (pilot flaw found, not falsified). Corrected: spread 24pp (k1=0.74,k5=0.98,k20=0.98), oracle 0.99 (raw 0.86 was EM echo artifact), beta 1.34 direction-robust, U-shape absent BUT k20 neutered by 900-char truncation bug + theory predicts ~0 dilution at short k. Needs cloud N=800 real NQ/DPR k to 50 full-context. File round9/pilot_idea6.md + ledger_P_I06.csv/I06b.csv.
## Pilot Idea 5 E2 CTO: PASS (logged above). Tool-IV rerun launched directly 2026-11-07 (no middleman).
- Idea 8 R1 ABLATE-RAG: SURVIVE to pilot. No randomized arms. Core 7.83 / Wtd 8.00, Indep 8.5. Kill: tau<2pp or shuffled>=gold or validity<90%.
## Pilot Idea 8 real-corpus DONE: SCOPED kill on twin leg only — tau=+32.5pp [24.8,40.2], shuffled 0.00<gold 0.40, A1 holds all 7 strata, C=0.075, P(EM|present)=73% / P(EM|absent)=0%. Twin validity 74%<90% trips disjunctive rule; v1 28% discarded as build error. Rescue: NER-matched twins on cloud; paper bounded to non-conflict retrieval. Files round9/pilot_idea8_realcorpus.md + ledger_P_I08b.csv (700 rows).
## Pilot Idea 8 DONE: PASS instrumentation (tau=100pp synthetic ceiling, shuffled 0, C=0.0 floor, validity 100%, distraction-follow 3%). Validates arms/A1/twins, NOT real-retrieval effect size. Next: noisy-corpus confirm on 7B/cloud. Files round9/pilot_idea8.md + ledger_P_I08.csv (600 rows).
- Idea 9 TASKCAL: KILLED on 0.5B grad-PC. Projection never beats scalar (best proj ECE 0.248 vs baseline 0.137); random matches (gap -0.0002). Confidence-subspace hypothesis not supported. File round9/pilot_idea9.md.
- Idea 4 M1 SCRIPT: PASS pilot (gap 11.82pp p=0.052; gain +6.36pp p=0.13). Phenomenon + alignment direction confirmed at N=200; CIs wide, cloud firms up. Eta theory stays downgraded — empirical paper survives. Core 6.63 / Wtd 6.90. Files round9/pilot_idea4.md + results_i04.csv (200 rows).
- Idea 2 E1 TURN-DECAY: PASS to pilot. No law+rotation prior. Core 8.25 / Wtd 8.33, Indep 9/10. Kill: drop<6pp or lambda<=0. R2 rule suspended (3-turn df=0), use logistic LRT.
## Pilot Idea 2 DONE 2026-11-07: PASS strong — UPDATED Core 8.375 / Wtd 8.375
## Pilot Idea 1 DONE 2026-11-07: PROXY-KILL then WHITE-BOX PASS — UPDATED Core 8.375 / Wtd 8.225
Text proxy dead (Delta 0.020). True 0.5B Kirchenbauer plant: Delta_d2=0.144, AUC=0.989, degrade 0.85 (McNemar b=86 c=1 p=1e-24), weak preserves (88% det), strong destroys (7%), control flat, monotone distances. Threshold structure real. Exp 7.0->8.0. Next: cloud 7B N=400/cell rho fit. Files round9/pilot_idea1_whitebox.md + pilot_idea1_wb/results_wb.json.
Ollama 4B 600 gens/3009s: neutral drop 15.0pp lam 0.461 p=0.0027; strict drop 14.7pp lam 0.481 p=0.0006. Exp 8.0->8.5. Next: 5-turn R2 on 7B/cloud.
## Verification DONE: CONFIRM PASS (independent Newton-Raphson + cluster SE, 0 mismatches, exclusions correct). File round9/pilot_idea2_verify.md.

## Final evidence-adjusted review (2026-11-08) — supersedes scan scores above
Current Round-8 weights; Core = mean(Novelty, Method, Math, Exp). Killed ideas intentionally omitted. Full verification: `round9/FINAL_SURVIVORS_REVIEW.md`.

| Rank | ID | Title | Core | Weighted | Status |
|---:|---|---|---:|---:|---|
| 1 | E2 | **Tool-Observation Sensitivity in Language-Model Agents: A Fixed-Prefix Causal Study** | 7.38 | 7.55 | Gate C: GO staged; 800 real prefixes, 3×7–8B, $70 cap |
| 2 | RANKCAL | **The Ranking Cost of Instruction Tuning: Quality Beyond Accuracy and Calibration** | 6.75 | 7.18 | direction only; N=2,500/TOST next |
| 3 | R1 | **Causal Retrieval Lift: Separating Retrieved Evidence from Answer Presence** | 6.65 | 6.83 | real effect; contamination/twin/conflict dropped |
| 4 | E1 | **Constraint Age or Constraint Load? Separating Two Causes of Multi-Turn Instruction Decay** | 6.63 | 7.10 | Gate D: valid 3-arm factorial; new pilot needed |
| 5 | Tool-IV | **The Causal Effect of Tool Use on Answer Accuracy: A Randomized Encouragement Study** | 6.63 | 6.98 | conditional; corrected bounds + hard tasks |
| 6 | M1 | **Bangla Across Scripts: Native-to-Romanized Transfer and Contrastive Alignment** | 6.00 | 6.50 | Gate F: n inflated; direction only; 9k/7B + native audit |
| 7 | T1 | **Watermark Survival Under Paraphrase: Surface Change, Not Semantic Distance** | 5.63 | 6.18 | Gate A: mean-radius theorem killed; Wang 2026 preempts; negative/measurement only |

Important: **all six gates are complete.** Gate A killed T1’s mean-radius theorem; only the non-identifiability + \(\bar p\) mechanism survives. Gate B cut R1 to 6.65/6.83 by removing invalid contamination/twin/conflict claims. Gate C passed E2 as a staged GO: 800 real τ-bench prefixes, 3 open-weight 7–8B models, 5 arms, MDE 0.20 logit/level, 6,554 eligible prefixes, $63 base/$70 cap. Gate D produced a valid 3-arm factorial for E1 (age⊥load; β_a MDE .076, β_k .060, Δ .084). Gate E made RANKCAL (N=2,500/TOST, MDE .028) and Tool-IV (4,500 calls, LATE MDE .089) conditional GO. Gate F cut M1 to 6.00/6.50 after finding inflated n and seed-confounded splits. **E2 is the final #1 at 7.38/7.55. No idea has a verified 8+ core.**
- Idea 10 RANKCAL: DIRECTIONAL PASS, n.s. at N=500. |dAUROC|=0.039 (DeLong CI covers 0, MDE~0.06 as predicted); kappa_Y 0.615; isotonic closes ECE gap 84% but rank gap persists post-isotonic — not pure level shift. IT more accurate yet worse-ranked. Needs N=1500 + 7B. Files round9/pilot_idea10.md + ledger_P_I10.csv (500 rows).
- Idea 7 Tool-IV: KEEP pilot-gated. No Z-nudge LATE prior. Core 7.19 / Wtd 7.35, Indep 8.5. Kill: gamma<0.15 or F<10 or width>0.6. Extensive vs Idea5 intensive.
Pending: Idea 1 T1 watermark.
## Round-9 COMPLETE 10/10
- Idea 1 T1 watermark: CONDITIONAL PASS. No E*(rho) prior; Zhang/Sadasivan point/impossibility only. Core 8.125 / Wtd 8.125, Indep 10/10. Paired mandatory (unpaired MDE 0.18-0.20). Kill: Delta<=0.05/AUC<0.6 or degrade<15pp.
Pilot running: Idea 2 3060. Queue: Idea 1 -> Idea 8.
## Pilot Idea 2 DONE 2026-11-07: PASS (strong)
Ollama qwen3.5:4b, 600 gens/3009s. Neutral drop 15.0pp [0.065,0.235], lam 0.461 p=0.0027; strict drop 14.7pp [0.066,0.228], lam 0.481 p=0.0006. Both >=6pp, CIs exclude 0. Next: 5-turn held-out R2 on 7B/cloud. File round9/pilot_idea2_ollama.md.
