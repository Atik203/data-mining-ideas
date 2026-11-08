# Round 10 Gate 05 — RANKCAL + Tool-IV Fixes

**Date:** 8 November 2026  
**Basis:** `round9/pilot_idea10.md`, `round9/pilot_idea7.md`, `round9/FINAL_SURVIVORS_REVIEW.md`, raw `pilot_i10_raw.csv` / `ledger_P_I07.csv`, `rankcal_stats.py`, `analyze_i07.py`.  
**Scope:** CPU-only re-analysis of the saved artifacts; no model loads, no pilot re-runs. Power/MDE numbers are design calculations from the pilot variance components and are labelled as such.

## 0. Verdicts

| Idea | Verdict | Exact next run | Binding MDE (80% power) | Stop condition |
|---|---|---|---|---|
| **RANKCAL** | **GO, conditional** | 2,500 MMLU items; Qwen2.5-7B base + Instruct (plus Llama-3.1-8B replication); 4 pre-registered MMLU groups; paired DeLong + TOST at ±0.03 | **0.028 AUROC** item-level; **0.036** subject-cluster-robust | Equivalence → null paper; inconclusive/parse/ceiling → stop |
| **Tool-IV** | **GO, conditional** | Calibration (400 items) then 4,500 calls: 1,500 items × 3 arms C/P/T = 1,800/900/1,800; hard tasks, p0 0.60–0.70; correct BP LP; placebo-δ sensitivity | **LATE 0.089** at γ=0.50; 0.099 at γ=0.45 | γ<0.35, or placebo |δ|>0.06, or LATE CI includes 0 with upper<0.10 |

Both gates are falsifiable and now have valid estimands. Neither justifies a GPU rental before its calibration stage passes.

---

# Part 1 — RANKCAL

## 1.1 What the pilot actually established

Independent recomputation on the 500 raw rows:

| Quantity | Pilot value | Recheck |
|---|---:|---:|
| Accuracy base → IT | 0.512 → 0.544 | 0.512 → 0.544 |
| AUROC base → IT | 0.7652 → 0.7259 | 0.7652088 → 0.7258611 |
| ΔAUROC (IT−base) | −0.0393 | −0.0393477 |
| DeLong SE | — | 0.0217990 |
| DeLong 95% CI | [−0.0821, +0.0034] | [−0.0820730, +0.0033776] |
| Item MDE (80%) | ≈0.061 | 0.0610718 |
| κ_all / κ_Y | 0.593 / 0.615 | 0.5931062 / 0.6154456 |
| ECE base → IT | 0.133 → 0.182 | Δ = +0.0485 |
| Isotonic ECE | 0.069 → 0.062 | closes 84% of gap |

The pilot's substantive problem is **power**, not direction. The point estimate is a 3.9-point AUROC drop; the 95% CI covers zero. The 57-subject bucket leg was untestable because the largest subject had 14 items (threshold was 15). That is a design error, not evidence of no subgroup heterogeneity.

Two further corrections matter for the next run:

1. **The 0.061 MDE is item-level.** A delete-one-subject jackknife on the same 500 rows gives SE 0.02342 (MDE 0.0656). Clustering by subject is real; the next design must report it.
2. **κ is a diagnostic, not a gate.** κ_Y = 0.615 rejects exact rank preservation, but the bound |ΔAUROC| ≤ (1−κ)/2 = 0.192 is far too loose to test a 0.03 effect. The exact rank-preservation lemma (strictly increasing transforms preserve AUROC) is already proved; the empirical claim is the size of ΔAUROC. Demote κ to a descriptive; do not use κ>0.8 or κ<0.9 as a pass/fail leg.

## 1.2 Coarser, pre-registered groups

Replace the 57-subject stratification with **four standard MMLU categories**, fixed before analysis:

| Group | Subjects (n) | Pilot n at N=500 | Expected n at N=2,500 |
|---|---:|---:|---:|
| STEM | 19 | 158 | ~790 |
| Humanities | 13 | 127 | ~635 |
| Social sciences | 12 | 102 | ~510 |
| Other / professional | 13 | 113 | ~565 |

The exact subject→category mapping is pre-registered verbatim in the analysis plan (standard MMLU taxonomy; Other = business_ethics, clinical_knowledge, college_medicine, global_facts, human_aging, management, marketing, medical_genetics, miscellaneous, nutrition, professional_accounting, professional_medicine, virology).

Rules for the group analysis:

- **Confirmatory test is pooled ΔAUROC**, not a group test. At N=2,500, a group has ~500–790 items; group-level SE is ≈0.020, so group MDE is ≈0.056. A group-level test cannot resolve a 0.03 effect and must not be a pass leg.
- **Falsification guardrail:** if any group with n≥400 has a **point estimate** ΔAUROC > +0.02, the word "systematic" is withdrawn and the group estimate is reported with its CI. A group CI covering zero is not a sign flip.
- **Secondary, exploratory:** baseline-difficulty quartiles (equal n per quartile, defined from base-model accuracy) and a group×Δ interaction via stratified bootstrap. Label exploratory; do not gate on it.

This fixes the pilot's untestable 57-bucket leg without pretending four groups are a powered heterogeneity test.

## 1.3 Variance components and the N≥1500 question

The pilot's item SE at N=500 is 0.02180. The delete-one-subject jackknife SE is 0.02342. Decompose:

- within-component at N=500: 0.02180² = 4.752×10⁻⁴
- between-subject component: 0.02342² − 0.02180² = 7.34×10⁻⁵ (between-subject SD 0.00857)
- cluster-robust SE at N: √(4.752×10⁻⁴ × 500/N + 7.34×10⁻⁵)
- floor as N→∞: 0.00857 (MDE 0.024), because there are only 57 subjects.

**N=1,500 does not buy 0.03.** The item-level MDE at N=1,500 is 0.0355; the cluster-robust MDE is 0.0427. N=2,000 is the minimum for item-level MDE 0.030; N=2,500 is the recommended design because it gives item-level TOST power 0.82 at ±0.03 and cluster-robust TOST power 0.85 at ±0.04.

| N | item SE | item MDE80 | cluster SE | cluster MDE80 | TOST ±.03 power (item/cluster) | TOST ±.04 power (item/cluster) | Superiority at −.039 (item/cluster) |
|---:|---:|---:|---:|---:|---:|---:|---:|
| 1,500 | 0.01267 | 0.0355 | 0.01523 | 0.0427 | 0.53 / 0.26 | 0.87 / 0.67 | 0.87 / 0.73 |
| 2,000 | 0.01076 | 0.0301 | 0.01387 | 0.0389 | 0.75 / 0.40 | 0.96 / 0.79 | 0.95 / 0.80 |
| **2,500** | **0.01001** | **0.0280** | **0.01298** | **0.0364** | **0.82 / 0.50** | **0.98 / 0.85** | **0.97 / 0.85** |
| 3,000 | 0.00904 | 0.0253 | 0.01236 | 0.0346 | 0.91 / 0.57 | 1.00 / 0.89 | 0.99 / 0.88 |
| 4,000 | 0.00790 | 0.0221 | 0.01153 | 0.0323 | 0.97 / 0.66 | 1.00 / 0.93 | 1.00 / 0.92 |
| 6,000 | 0.00650 | 0.0182 | 0.01063 | 0.0298 | 1.00 / 0.76 | 1.00 / 0.97 | 1.00 / 0.96 |

TOST power is at true Δ=0; superiority power is at the pilot point −0.0393. α=0.05 two-sided for superiority; TOST uses two one-sided 0.05 tests (90% CI). Item-level is primary; cluster-robust is the conservative sensitivity. If the journal demands cluster-robust TOST at ±0.03, N=6,000 gives 0.76 power and N≈7,500 gives 0.80 — a separate decision, not a free lunch.

## 1.4 TOST / equivalence framing

Pre-register two questions with gatekeeping:

1. **Superiority:** H₀: Δ=0 vs H₁: Δ≠0, two-sided α=0.05, 95% CI.
2. **Equivalence:** H₀: |Δ|≥0.03 vs H₁: |Δ|<0.03, two one-sided α=0.05 tests; equivalently, the 90% CI ⊂ (−0.03, +0.03).

Gatekeeping: test superiority first. If significant, report the estimate and **do not** claim equivalence; if not significant, test equivalence. This controls the familywise error for "any effect" and prevents claiming equivalence when a nonzero effect is detected.

Decision table:

| 95% CI | 90% CI | Conclusion |
|---|---|---|
| excludes 0, entirely < −0.03 | — | meaningful negative tax confirmed (non-equivalence) |
| excludes 0, within (−0.03, 0) | — | statistically nonzero but small tax; report magnitude, do not call meaningful |
| includes 0 | ⊂ (−0.03, +0.03) | equivalence: negligible rank change; publish null/equivalence |
| includes 0 | not ⊂ (−0.03, +0.03) | inconclusive; stop, do not spend more |
| excludes 0, entirely > +0.03 | — | rank gain; report as opposite-direction finding |

Do not interpret "failure to reject Δ=0" as equivalence; the TOST is the equivalence test. Report both the 90% CI (TOST) and the 95% CI (superiority).

**Fix the margin before unblinding.** Pre-register ±0.03 if item-level inference is primary, or ±0.04 if subject-cluster-robust inference is primary. Do not switch margins after seeing Δ; the two margins answer different questions and the choice must be made in advance.

Optional Bayesian ROPE (secondary): flat prior on Δ; at N=2,500 and the pilot point, P(Δ<−0.03)=0.82 item / 0.76 cluster, and P(|Δ|<0.03)=0.18 / 0.24. This favours a meaningful tax roughly 3–4:1, but it is not a substitute for TOST.

## 1.5 Exact next-run specification

**Design.** 2,500 MMLU test items, subject-stratified proportional allocation (~44/subject), pre-registered seed 0. Run a 200-item calibration first.

**Models.** Qwen2.5-7B base + Qwen2.5-7B-Instruct primary; Llama-3.1-8B base + Instruct replication. 4-bit, T=0, fixed completion prompt, no chat template for the primary base/IT comparison. Confidence = max softmax over A/B/C/D answer-token surface variants, renormalized; store raw top-4 logits. Add a **format-control arm**: IT model with its chat template on 500 items; if the sign of ΔAUROC flips, the tax is format-dependent and must be reported as such.

**Benchmarks.** MMLU primary; ARC-Challenge (1,000) and GSM8K (1,000) secondary replication (MDE ≈0.044 item-level, so label replication, not confirmatory). If 7B MMLU base/IT accuracy exceeds 0.85, switch the primary to MMLU-Pro/ARC and keep MMLU secondary; pre-register the switch rule.

**Statistics.** Paired DeLong (Sun–Su) with subject-stratified bootstrap 10,000; ΔAURC/ΔECE 10,000 bootstrap; 5-fold CV isotonic (not 2-fold) for post-hoc calibration; κ_Y diagnostic only; TOST per §1.4. Report the item-level and cluster-robust results side by side; the paper's primary is item-level, with cluster-robust as the conservative sensitivity.

**Compute.** 2,500 × 2 models + 2,000 × 2 secondary ≈ 9,000 generations. 7B 4-bit on a 24GB GPU ≈ 4–8 h; <$30 cloud. No 6GB 3060 for 7B.

**Stop conditions.**
1. Parse failure >5%, or base/IT accuracy outside [0.35, 0.85] → fix prompt/task before analysis; do not interpret.
2. 95% CI includes 0 and 90% CI ⊂ ±0.03 → equivalence; stop the tax story and write the equivalence/null paper.
3. 95% CI includes 0 and 90% CI not ⊂ ±0.03 → inconclusive; stop; no further spend.
4. 95% CI excludes 0 and point ≤−0.03 → tax confirmed; write.
5. 95% CI excludes 0 and point in (−0.03, 0) → small but real tax; write as a measurement result; do not claim practical significance.
6. Any group with n≥400 has point Δ>+0.02 → withdraw "systematic"; report heterogeneity.

(κ_Y is diagnostic only; it is not a stop condition.)

---

# Part 2 — Tool-IV

## 2.1 What the pilot actually established, and what was wrong

Raw recheck (N=150):

| Quantity | Pilot | Recheck |
|---|---:|---:|
| Z=0: D, Y | 0, 0.960 | 0, 0.960 |
| Z=1: D, Y | 0.267, 0.987 | 0.2667, 0.9867 |
| ITT | +0.027 | +0.0267, 95% CI [−0.0247, +0.0781] |
| γ | 0.267 | 0.2667 |
| First-stage F | 26.9 | 27.3 (one-sided variance) |
| Wald LATE | +0.10 | +0.100, 95% CI [−0.098, +0.298] |
| One-sided bounds | [−1.0, +0.15], width 1.15 | **correct [0, +0.15], width 0.15** |
| Balke–Pearl | [0.013, −1.37], inverted | **correct standard BP ATE [−0.693, +0.040], width 0.733** |

Three corrections:

1. **The one-sided lower bound is 0, not −1.** The pilot's closed form mixed the ITT/γ Wald expression with the one-sided bound. The correct no-exclusion assignment-effect bound is derived in §2.3.
2. **The BP output was inverted because the closed-form was wrong.** Replace it with the LP in §2.3. The correct standard BP ATE bound is [−0.693, +0.040], not [0.013, −1.37].
3. **The old "width>0.6" kill conflates two objects.** The BP ATE width is 0.733; the one-sided assignment-effect width is 0.15; the LATE is point identified at 0.10 under exclusion. A single width threshold cannot adjudicate all three. The pilot's "bounds vacuous at this ceiling" is backwards: the ceiling made the assignment-effect bound narrow, because width = (1−p0)/γ when p0+γ≥1. On harder tasks (lower p0), the no-exclusion bound **widens** unless γ rises. Harder tasks help headroom and ITT, not the no-exclusion bound.

## 2.2 Estimand hierarchy (pre-register)

1. **ITT of the nudge:** E[Y|T]−E[Y|C]. No assumptions. Always reported.
2. **LATE of tool use:** exclusion + monotonicity + one-sided compliance (D=0 in C and P by construction); τ=ITT/γ. Primary causal estimand.
3. **Sharp bounds:** standard BP ATE bounds under randomization + exclusion; one-sided assignment-effect bounds as the no-exclusion robustness object. Report both, never call the ATE bound "LATE."
4. **Exclusion sensitivity:** placebo arm δ_p; LATE(δ)=(ITT−δ)/γ; breakdown δ*.

## 2.3 Correct BP / one-sided sharp bounds: exact algorithm

### Standard BP LP

State space: (y₀, y₁, d₀, d₁) ∈ {0,1}⁴, where y_d = Y(d), d_z = D(z); observed Y = y_{D(z)}. This is the standard IV/exclusion model. LP:

- variables π_s ≥ 0, Σ_s π_s = 1;
- for z=0,1 and (y,d) ∈ {0,1}²: Σ_{s: D(z)=d, y_d=y} π_s = P(Y=y, D=d | Z=z);
- ATE objective Σ_s π_s (y₁−y₀); bounds = min/max;
- LATE: restrict to complier states d₀=0, d₁=1; objective Σ_{comp} π_s (y₁−y₀)/γ, with γ=P(D=1|Z=1) identified;
- one-sided design: drop d₀=1 states. Do **not** add the extra "no effect at zero" restriction y₀=y₁ for never-takers; that is not the exclusion restriction, and it collapses the ATE to the ITT.

Use `scipy.optimize.linprog(method="highs")`, bounds (0,1). Unit tests:

1. Full compliance (D=Z): LATE=ATE=ITT; bounds point.
2. No noncompliance (γ=0): LATE undefined; ATE=ITT; bounds point.
3. One-sided + exclusion: LATE=ITT/γ; LP returns point.
4. Simulated principal-stratum model with known ATE/LATE: LP bounds contain the truth; lower≤upper in every replicate.
5. Defiers / monotonicity violation: LP without monotonicity widens; one-sided design makes defiers infeasible.
6. Bootstrap: 2,000 reps; every rep lower≤upper; report the 2.5/97.5 percentiles of the bound endpoints as a **CI for the identified set**, not a CI for the parameter.

Pilot results: BP ATE [−0.693, +0.040], bootstrap 95% CI [−0.795, +0.088]; exclusion-restricted LATE point +0.100; one-sided assignment-effect [0, +0.150], bootstrap CI [0, +0.389]. The assignment-effect bound is not "vacuous" in the point sense; at N=150 its sampling CI is wide, which is a precision problem, not a structural one.

### One-sided assignment-effect bound (no exclusion)

Define p₁₁=P(Y=1,D=1|Z=1), p₀=P(Y=1|Z=0), γ=P(D=1|Z=1). The no-exclusion bound on
τ_A = E[Y(1,1) − Y(0,0) | complier] is:

- lower = p₁₁/γ − min(1, p₀/γ)
- upper = p₁₁/γ − max(0, (p₀−(1−γ))/γ)

At the pilot: p₁₁=0.2667, γ=0.2667, p₀=0.96 → [0, 0.15]. Width = (1−p₀)/γ when p₀+γ≥1. This is a **no-exclusion robustness object**, not the LATE; under exclusion it collapses to the Wald point 0.10. Report it as τ_A, never as "LATE bounds."

## 2.4 Harder tasks and stronger first stage

**Task set (1,500 items):** MATH-500 levels 3–5 (400), GSM8K hard subset (300), MuSiQue 2–3 hop (400), HotpotQA hard/distractor (400). Target base accuracy p₀ 0.60–0.70; screen with a 400-item calibration (200 math, 200 factual). If p₀>0.80, replace with harder items (MATH level 5, MuSiQue 3-hop); if p₀<0.50, the task may be too hard for the tool to help; rebalance.

**Nudge:** explicit tool affordance in the system prompt + one worked example + "Use the tool when it will improve your answer." Active arm: working Python calculator (math) or retrieval over five supplied passages (factual). Placebo arm: identical text, tool disabled (harness returns "tool unavailable"). Control: no tool mention.

**Randomization:** 3 arms, 2:1:2 (C/P/T), stratified by task and difficulty tier. Each item appears once (between-item), so the analysis is a stratified ITT; if within-item pairing is preferred, 1,500 items × 3 = 4,500 calls with item fixed effects, but the between-item design is simpler and the power table below assumes independent arms.

**D definition:** primary D=1 if the tool is called and its output inserted; secondary D=1 if the final answer uses the tool output. Pre-register both; the primary LATE uses the primary D. Target γ≥0.50; if calibration γ<0.35, stop and redesign the nudge.

## 2.5 Placebo-nudge exclusion sensitivity

- δ_p = E[Y|P]−E[Y|C]; D=0 in both P and C by construction, so δ_p is a direct effect of the nudge text (and prompt format), not of tool use.
- Sensitivity: LATE(δ)=(ITT−δ)/γ for δ∈[−c,c], c=max(|δ_p|+1.96 SE_p, 0.02). Report the curve and the breakdown δ* = smallest |δ| at which the 95% CI for LATE(δ) includes 0.
- Decision: if the δ_p 95% CI is within ±0.03 and δ*>0.08, exclusion is supported. If |δ_p|>0.06 with CI excluding 0, exclusion is violated; pivot to an ITT paper and drop the LATE claim. If δ*≤0.05, the LATE is fragile even if δ_p is small; downgrade.

At N=4,500 (900 placebo, 1,800 control), SE_δ=0.0195, 95% halfwidth 0.038; the design can detect |δ|≈0.055 at 80% power. If the target LATE is 0.10, ITT=0.05 and δ*=0.05; the placebo CI upper 0.038 is just below δ*, so the LATE is only moderately robust. State this openly. If robustness is the goal, increase placebo/control to 1:1 (N=5,400) or target a larger LATE.

## 2.6 Power / MDE

Binary Y, 2:1:2 allocation, α=0.05 two-sided, 80% power, p₀=0.65, no baseline covariate (conservative).

| N calls | C/P/T | SE_ITT | MDE_ITT | MDE_LATE γ=.45 | γ=.50 | γ=.55 | placebo SE_δ | 95% δ halfwidth |
|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| 3,000 | 1,200/600/1,200 | 0.0195 | 0.0546 | 0.121 | 0.109 | 0.099 | 0.0239 | 0.047 |
| 3,600 | 1,440/720/1,440 | 0.0178 | 0.0498 | 0.111 | 0.100 | 0.090 | 0.0218 | 0.043 |
| **4,500** | **1,800/900/1,800** | **0.0159** | **0.0446** | **0.099** | **0.089** | **0.081** | **0.0195** | **0.038** |
| 6,000 | 2,400/1,200/2,400 | 0.0138 | 0.0386 | 0.086 | 0.077 | 0.070 | 0.0169 | 0.033 |

At N=4,500, γ=0.50, true LATE=0.10: SE_LATE≈0.032, 95% CI ±0.063, power ≈0.88. At γ=0.45, power ≈0.81. At true LATE=0.08, power ≈0.63 (γ=0.45) to 0.72 (γ=0.50); the calibration stage should decide whether to extend to 6,000. First stage at γ=0.50, n_T=1,800: SE_γ=0.0118, F≈1,800; weak-IV risk is solved by design.

The old pilot's LATE MDE at N=150 was ≈0.28; the new design is ≈3.1× tighter. That is the scale-up.

## 2.7 Exact next-run specification and stop conditions

**Stage 0 — calibration (400 items, 800 calls).** C vs T only; same model family as the main run (7B/8B). Measure p₀, γ, parse rate. Gates: p₀∈[0.55, 0.80], γ≥0.35, parse failure <5%. If fail, stop/redesign; no main run.

**Stage 1 — main (4,500 calls).** 1,500 items × 3 arms C/P/T allocated 2:1:2 (1,800/900/1,800), stratified by task and difficulty tier. Model: Qwen2.5-7B-Instruct or Llama-3.1-8B-Instruct, 4-bit, T=0, fixed prompt. Record full trace (tool call, tool output, final answer). No prompt changes between arms except the nudge/tool availability.

**Pre-registered calibration-to-N rule.** Before Stage 0, fix: if calibration γ<0.45, extend the item pool to 2,000 and run 6,000 calls (2,400/1,200/2,400; MDE LATE 0.077 at γ=.50); if γ≥0.45, run 1,500 items / 4,500 calls. The calibration outcome is γ and p₀ only; no main-run outcome is used to choose N.

**Outcomes.** Binary exact match (math: final numeric answer; factual: exact entity/answer string). Pre-register parsing; no LLM judge. Secondary: confidence, answer length, tool-call count.

**Analysis.** Stratified ITT (weighted average of stratum differences), γ, Wald LATE; item-level bootstrap 10,000; BP LP per §2.3; placebo δ; report ITT/LATE/bounds/δ curve. Primary contrast T vs C; placebo P is for exclusion sensitivity only.

**Compute/cost.** 5,300 calls total (800 calibration + 4,500 main); 7B 4-bit on 24GB ≈ 3–6 h; <$30 cloud.

**Stop conditions.**
1. Calibration p₀ outside [0.55, 0.80], or γ<0.35, or parse failure >5% → stop/redesign.
2. Main γ<0.35 → stop; weak first stage; do not interpret LATE.
3. Placebo |δ_p|>0.06 with 95% CI excluding 0 → exclusion violated; pivot to ITT paper; drop LATE.
4. LATE 95% CI includes 0 and the upper bound <0.10 → no meaningful effect; stop.
5. LATE 95% CI includes 0 and the upper bound ≥0.10 → inconclusive; extend to 6,000 or stop depending on budget; do not claim a null.
6. LATE 95% CI excludes 0 and lower bound ≥0.05 → GO to paper; report bounds and sensitivity.
7. LATE 95% CI excludes 0 and lower bound in (0, 0.05) → small positive effect; report, do not overclaim.
8. BP LP lower>upper in any replicate, or any unit test fails → stop; code bug; do not report bounds.
9. LATE CI width >0.25 → underpowered; do not claim a precise effect; either extend to 6,000 or stop.

---

# 3. Path and final verdicts

**RANKCAL — GO conditional.** Run 2,500 MMLU items on 7B base/IT, four pre-registered MMLU groups, paired DeLong, TOST at ±0.03 item-level (±0.04 cluster-robust). MDE 0.028 item / 0.036 cluster. If the tax is confirmed, the paper is "Instruction tuning taxes ranking, not just calibration." If equivalence is established, the paper is "No meaningful rank tax at 7B." Both are publishable; the second requires the TOST CI, not a null p-value. Stop if the 90% CI is inconclusive or the group guardrail fires.

**Tool-IV — GO conditional.** Calibration then a 4,500-call three-arm hard-task run; correct BP LP; placebo-δ sensitivity. MDE LATE 0.089 at γ=0.50, 0.099 at γ=0.45. If the LATE CI excludes 0 and exclusion is robust, the paper is a causal tool-use study with sharp bounds and a placebo. If not, it is an ITT/measurement paper or a kill. Do not rent a GPU before Stage 0 passes.

**No score inflation.** Both gates now have a valid estimand, a power calculation, and a stop condition. The main corrections are: RANKCAL's 57-subject leg was untestable and its N=500 MDE was 0.061; Tool-IV's BP code was inverted and its "vacuous bounds" diagnosis was an artifact of the wrong object and the ceiling.

---

## Appendix A — recomputation details

**RANKCAL raw.** 500 rows; 57 subjects; base/IT accuracy 0.512/0.544; AUROC 0.7652088/0.7258611; DeLong Δ −0.0393477, SE 0.0217990, CI [−0.0820730, +0.0033776], MDE 0.0610718; κ_Y 0.6154456, κ_all 0.5931062; ECE 0.1333/0.1818. Item bootstrap SE: n=500 0.02170; 1,500 0.01267; 2,000 0.01076; 2,500 0.01001; 3,000 0.00904; 4,000 0.00790; 6,000 0.00650. Delete-one-subject jackknife SE 0.02342; between-subject SD 0.00857. Group counts at N=500: STEM 158, Humanities 127, Social 102, Other 113; group Δ: −0.0466, −0.0701, −0.0422, −0.0190 (all negative point estimates; no group CI excludes 0).

**RANKCAL group mapping (pre-register verbatim).** STEM (19): abstract_algebra, anatomy, astronomy, college_biology, college_chemistry, college_computer_science, college_mathematics, college_physics, computer_security, conceptual_physics, electrical_engineering, elementary_mathematics, high_school_biology, high_school_chemistry, high_school_computer_science, high_school_mathematics, high_school_physics, high_school_statistics, machine_learning. Humanities (13): formal_logic, high_school_european_history, high_school_us_history, high_school_world_history, international_law, jurisprudence, logical_fallacies, moral_disputes, moral_scenarios, philosophy, prehistory, professional_law, world_religions. Social sciences (12): econometrics, high_school_geography, high_school_government_and_politics, high_school_macroeconomics, high_school_microeconomics, high_school_psychology, human_sexuality, professional_psychology, public_relations, security_studies, sociology, us_foreign_policy. Other/professional (13): business_ethics, clinical_knowledge, college_medicine, global_facts, human_aging, management, marketing, medical_genetics, miscellaneous, nutrition, professional_accounting, professional_medicine, virology.

**Tool-IV raw.** N=150; Z=0 n=75, Y=0.960; Z=1 n=75, D=0.2667, Y=0.9867; ITT 0.0267; γ 0.2667; F 27.3; Wald 0.100; BP ATE [−0.6933, +0.0400], bootstrap 95% CI [−0.795, +0.088]; one-sided assignment-effect [0, +0.150], bootstrap 95% CI [0, +0.389]. The LP uses 16 states, `scipy.optimize.linprog(method="highs")`, constraints as §2.3; unit tests as listed.
