# E2 scale-up gate — confirmatory fixed-prefix observation-sensitivity study

**Date:** 8 November 2026  
**Basis:** `round9/pilot_idea5.md`, `round9/05_idea5_cto.md`, `round8/06_deep_empirical_85.md`, `round9/FINAL_SURVIVORS_REVIEW.md`. CPU-only gate; no model runs, no paid API, no pilot re-run.  
**Decision:** **GO, staged, hard cap $70.** NO-GO to a “universal law” or model-ranking claim unless the slope replicates and the recovery half-life is identifiable.

---

## 0. Bottom line

- **Design:** 800 real prefixes (400 τ-bench airline, 400 retail), 3 open-weight 7–8B models, 5 observation arms (verbatim JSON anchor + four prose levels s=0…3), 4-step recovery. Paired within prefix; target models do not generate the prefixes. This is a design pass, not a score increase; E2’s current 7.38/7.55 stands until data arrive.
- **Slope power:** MDE **0.20 logit/level per model** at N=800 (OR 1.22), 80% power, α=.05 two-sided; pooled across models ≤0.15 (conservative 0.20). The pilot-implied b≈0.8 is ~4× the MDE, so the slope test has >99% power if the pilot direction survives real prefixes.
- **Recovery power:** with a 20% switch rate at s=3, 160 switchers/model; 95% CI on t½ **±20% per model, ±11% pooled**. Between-model and between-severity t½ differences need **32–50%**; the study estimates a half-life, it does not rank models.
- **Harvest supply is not the risk:** the public τ-bench historical trajectories contain **13,605** candidate post-tool decision points; after excluding profile/PII tools and retaining observations whose values appear in the donor’s continuation (value-used proxy), **6,554** remain across all **165** task–domain pairs (min 7, median 38; airline median 31). A cap of 10/task yields 1,646; the airline arm is the binding constraint (50 tasks × 10 = 496 max, enough for 400). 800 is feasible.
- **Cost:** 35–45 GPU-hours on a 24GB rental (**$15–22**), storage **$2–3**, contingency **$3–5**; **base $23, hard cap $70**. Human labeling is done by the team at no cash cost.
- **Gates:** G0 harvest ≥880 eligible + 40-item audit ≥90%; G1 calibration parse ≥95%, placebo jitter ≤20%, s=3 switch 10–80%; G2 stop at $22/45 GPU-hours; G3 slope replicated in ≥2/3 models; G4 ≥50% recovery events and exponential not rejected.

---

## 1. What the pilot established and what it did not

The pilot (N=60, qwen3.5:4b, synthetic WebArena-lite/τ-style prefixes, letter-coded next action) found:

| Quantity | Pilot |
|---|---:|
| Consult rate | 54/60 = 90.0% |
| Placebo jitter P-vs-T | 0/60 = 0% |
| Error switch E-vs-T | 5/60 = 8.3% |
| Gap, 95% CI | 8.3 pp [1.3, 15.3] |
| McNemar | b=5, c=0; exact p=.0625 |
| Human spot-check | 12/12 script labels agree |

This is a **screening signal**, not a dose-response law. Four limitations drive the confirmatory design:

1. **N=60 and p=.0625.** The CI is compatible with a 1.3 pp effect; the pilot cannot distinguish a real graded law from a handful of flips.
2. **Letter coding is conservative.** The spot-check found silent fact propagation (wrong price/time/dose answered with “A”); the pilot’s 8.3 pp is an action-level lower bound, not content sensitivity.
3. **One model, synthetic prefixes, no real tool outputs.** The effect may be an artifact of template prose or of qwen3.5:4b.
4. **No recovery data.** The half-life law in the original E2 proposal is entirely untested; the confirmatory study must treat it as a pre-specified estimation target, not a confirmed law.

The confirmatory object is therefore: **the causal effect of observation content on the next decision, graded by severity, across real prefixes, real tool outputs, multiple models and tasks; and the time to recover from a bad observation.** The extensive margin (whether to use tools) is conditioned away by harvesting post-tool prefixes and is not a claim of this study.

---

## 2. Design

### 2.1 Units, models, tasks

- **Unit:** a frozen message prefix ending immediately after a real tool observation. The prefix includes the τ-bench system policy, the user turns, the donor assistant turns, and all prior tool calls/results. The target model produces the next decision.
- **Primary sample:** 800 prefixes, 400 airline + 400 retail. Harvest 880, hold 80 as reserve; freeze the 800 before seeing any target-model outcome.
- **Models (primary):**
  | Model | Params | License | Role |
  |---|---:|---|---|
  | Qwen2.5-7B-Instruct | 7B | Apache 2.0 | primary family |
  | Mistral-7B-Instruct-v0.3 | 7B | Apache 2.0 | second family |
  | Llama-3.1-8B-Instruct | 8B | Llama 3.1 Community | third family; accept license |
  - 4-bit AWQ, vLLM, greedy decoding (T=0), fixed seed, one model at a time on 24GB. A 3B model is an optional extension only if the primary run finishes under 25 GPU-hours; it is not in the budget or the power claim.
- **Tasks:** τ-bench airline and retail. These are two different tool/policy domains, not two paraphrases of one task. The model × task design is fully crossed; prefixes are nested within task and shared across models.
- **Donor separation:** prefixes come from the public gpt-4o and claude-3.5-sonnet historical trajectories. The target models are open-weight 7–8B models and never generate the prefix. This avoids donor–target circularity. If Llama-3.1 access is unavailable, substitute InternLM2.5-7B-Chat or Yi-1.5-9B-Chat (both open-weight) and pre-register the substitute before calibration.
- **Scope:** open-weight 7–8B models, next-decision sensitivity and 4-step recovery. End-to-end task success and frontier models are out of scope for this gate; they are a follow-up, not a hidden claim.

### 2.2 Arms and severity manipulation

All four severity arms use the **same prose rendering** of the observation; only the content differs. The verbatim JSON arm is an anchor for format effects and is not in the primary slope.

| Arm | Observation | Decision relevance | Role |
|---|---|---|---|
| V | exact real tool output, raw JSON | — | format/jitter anchor; secondary total effect |
| s=0 | faithful prose paraphrase of V (all values preserved) | none | placebo; intercept/jitter |
| s=1 | one non-critical field changed or omitted | field not used in the donor’s factual continuation | negative control |
| s=2 | one decision-critical field changed to a plausible wrong value | changes the correct action if trusted | dose |
| s=3 | critical field changed to an extreme/contradictory value plus a second inconsistency (e.g. “cancelled” while seats are available; “non-refundable” inside the refund window; wrong entity/policy) | changes the correct action and should be flagged | dose |

**Construction rules**

- The prose renderer is deterministic (a key–value/bullet template), not an LLM paraphrase. No generated text enters the manipulation. A script verifies that the s=0 value set is identical to V; any information loss excludes the prefix.
- The perturbation record stores the JSON path, true value, false value, criticality label, and the expected action change. This makes the outcome label automatic rather than judge-dependent.
- s=1 is a true negative control: if the model’s decision moves when a non-critical field changes, the field was misclassified or the model is hypersensitive; the human audit catches both.
- s=2/s=3 values are plausible, not absurd. The s=3 contradiction is designed to be detectable by a careful reader; the calibration gate checks that it is not a ceiling.
- **Severity is ordinal, not interval.** s=3 is intentionally compound (critical error + contradiction); the linear slope is a summary, and the s=2 vs s=3 contrast is reported separately so the compound component is not hidden. Equal spacing is a modeling choice tested by the linearity check.
- **Recovery regimes:** primary = **transient** injection (only the injected observation is wrong; later tool calls return truth). Secondary = **persistent** injection on 25% of prefixes (the same field stays corrupted; recovery requires flagging/working around, not just re-querying). This separates “re-query” from “error correction.”

### 2.3 Outcomes

For prefix p, model m, arm a:

- **Decision signature** = tool name + canonicalized JSON args, or the final answer’s decision-critical slots + action verb. Canonicalization: sorted keys, numeric normalization, lower-cased entities.
- **Y** (any sensitivity) = signature differs from the verbatim V continuation.
- **Y^U** (silent propagation) = the false value of a **decision-critical** field appears in the decision-relevant content/args and there is no flag. For s=1 the perturbed field is non-critical and Y^U is not scored; s=1 remains a negative control.
- **Y^F** (detection) = explicit flag, doubt, “possible error”, “inconsistent”, “verify”, “cannot trust”.
- **Y^A** (action switch) = tool/args differ from V (re-call, re-query, abstain).
- Decomposition: **Y = Y^A ∨ Y^F ∨ Y^U**; **silent propagation = Y^U and not Y^A and not Y^F**.

**Recovery**

- Risk set for R1 and R2: prefixes whose decision moved at the injected step (Y^U=1 or Y^A=1) and that did not flag at that step, separately for s=2 and s=3. Prefixes that only flagged at the injected step are already detected and are coded as h=0 in the detection analysis; they are not at risk for a later recovery.
- Continue the episode for H=4 steps with the real environment; recovery at h is the first step where the signature matches V (**R1: correct decision**) or where the model explicitly flags the observation (**R2: detection**).
- Censored at H=4 if neither occurs.
- Primary recovery endpoint: R1 under transient injection at s=3. Secondary: R2, persistent, s=2.

### 2.4 Estimands and models

**Primary slope**

\[
\operatorname{logit} P(Y_{p,m,s}=1)=\alpha_{p,m}+\beta_m s,\qquad s=0,1,2,3
\]

- Conditional (fixed-effects) logistic regression, stratified by prefix, linear dose; cluster-robust SE by prefix; fit separately for each model. β_m is the log-odds change per one-level increase in severity.
- Pooled β: stratify by prefix × model, cluster by prefix; fixed-sequence gate (pooled first, then per-model Holm).
- The s=0 arm is the placebo; the intercept absorbs format jitter. A sensitivity analysis fits s=1…3 only and excludes the placebo.
- Linearity check: add a quadratic term or categorical s; if the linearity test rejects, report tier-specific ORs and the slope as a summary, not as the law.
- Secondary: Y^U, Y^F, Y^A; s=3 vs s=1 McNemar; task-specific slopes; model × severity and task × severity interactions (exploratory).

**Recovery half-life**

- Discrete-time survival with interval censoring: P(recover at h | not yet) = 1 − exp(−μ) under the exponential model; t½ = ln2/μ.
- Fit by maximum likelihood; report Kaplan–Meier and restricted mean recovery time (RMST) over H=4 regardless.
- Test the exponential assumption with a Weibull shape parameter (LRT). If shape ≠ 1 (p<.01), do not report t½ as a law; report Weibull/RMST and label t½ exploratory.
- t½ is identifiable only if ≥50% of the risk set recovers by H=4; otherwise report a lower bound and the recovery curve.

**Multiplicity and claim hierarchy**

1. Pooled slope β > 0 (primary confirmatory test).
2. Per-model slopes β_m > 0 (Holm across three).
3. Silent propagation Y^U > 0 and its share of Y (secondary, descriptive).
4. Recovery t½ estimate with CI (estimation, not a significance gate).
5. Model/task heterogeneity (exploratory; MDE stated).

No claim of a universal constant, model ranking, or task invariance is made unless the relevant interaction/CI supports it.

---

## 3. Prefix harvest protocol

### 3.1 Source

τ-bench historical trajectories, pinned commit, MIT license: `historical_trajectories/gpt-4o-{airline,retail}.json` and `sonnet-35-new-{airline,retail}.json`. The files contain 1,980 full trajectories across 165 task–domain pairs and include the actual tool calls and tool outputs emitted by the τ-bench environment. “Real tool output” here means **emitted by the benchmark’s tool implementation against the task database and verified by replay**, not LLM-generated prose or a template.

### 3.2 Steps

1. **Parse.** Iterate each trajectory; select every assistant message immediately following a `tool` message. Reconstruct the exact prefix (system + all prior messages). Record trajectory ID, task_id, trial, donor, domain, step index, tool name, raw observation, and the factual continuation.
2. **PII scrub.** Exclude observations from `get_user_details`/profile tools (3,692 candidates). Replace names, emails, addresses, phone numbers, and payment IDs in the remaining observations with stable synthetic tokens; preserve structure and values needed for the decision. If the decision-relevant content is itself PII, exclude the prefix.
3. **Decision relevance.** Flatten scalar values from the observation; check which appear in the factual continuation (normalized string/number match). Mark fields used/unused. Exclude prefixes with no used field. This is the automatic criticality label that makes s=1/s=2 construction possible.
4. **Replay verification.** Load the task DB, execute the prefix’s tool calls from the initial state, and compare the last observation to the historical one (hash + normalized values). Keep only ≥98% matches. This guarantees that the observation is a real environment output and that recovery can be simulated.
5. **Stratified sample.** Cap at 10 prefixes per task–domain (165 × 10 = 1,650 max; empirical 1,646). The airline domain is the binding arm: 50 tasks × 10 = 496 max, enough for 400 plus attrition. Stratify by domain (400/400), donor (gpt-4o/sonnet), step index (1–8+), and tool family. Random seed pre-registered. Sample 880; freeze 800 primary + 80 reserve.
6. **Freeze.** Store prefix IDs, message hashes, observation hashes, perturbation records, arm assignment, and the renderer version. No changes after seeing target-model outcomes.
7. **Audit.** A human reads 40 random prefixes: eligibility, PII, decision relevance, replay match. Target ≥90% (≥36/40); if lower, revise and re-sample before G1.
8. **Donor check.** Report the donor distribution in the final sample; if one donor dominates a task, rebalance. Do not use the target models to generate or repair prefixes.

### 3.3 Empirical yield (run on the actual files, 8 Nov 2026)

| Source | Trajectories | Candidates | JSON-parseable |
|---|---:|---:|---:|
| gpt-4o airline | 200 | 1,021 | 941 |
| gpt-4o retail | 460 | 3,198 | 2,486 |
| sonnet airline | 400 | 2,525 | 2,419 |
| sonnet retail | 920 | 6,861 | 5,472 |
| **Total** | **1,980** | **13,605** | **11,318** |

After excluding profile/PII tools and keeping only value-used observations: **6,554 eligible** (airline 1,593; retail 4,961) across all **165** task–domain pairs. Eligible per task: min 7, median 38, max 121 (airline median 31; retail median 40). A cap of 9/task gives **1,483** (airline 448; retail 1,035); the airline arm is the binding constraint, and 448 covers the 400 target plus attrition. The 800 target is 12% of the eligible pool and 54% of the capped pool.

### 3.4 Contamination and external validity

The τ-bench tasks are public and may be in the target models’ training data. The fixed-prefix *perturbation* is novel, so the within-prefix contrast remains valid, but baseline reliance may be inflated. Two safeguards:

- Report the **verbatim-arm accuracy** and the s=0 jitter; if the model ignores the observation entirely (flat s=1…3), the sensitivity is unidentified and the study stops at G1.
- Add an optional **120-prefix template-transfer arm** (new τ-style entities, locally constructed) only if the main run is under budget. If its slope differs materially from τ-bench, the paper reports a domain boundary, not a universal law. It is not in the primary budget.

---

## 4. Label validation

### 4.1 Severity manipulation

- **Sample:** 120 observations, 30 per level, stratified by domain and tool family; 3 raters (author + 2 co-authors), blind to arm label.
- **Ratings:** (a) decision relevance 1–5 (“could this change the correct action?”), (b) severity 1–5, (c) plausibility/detectability 1–5.
- **Pass criteria:** mean severity monotone across s=0…3 (Jonckheere–Terpstra p<.01); Krippendorff α ≥ .75; ≥90% of s=1 rated non-decision-changing; ≥90% of s=2/s=3 rated decision-changing; s=3 not rated “absurd” by >20%.
- **Failure:** collapse s=1 into s=0, or redefine s=2/s=3 and re-audit; if s=2 and s=3 cannot be separated, the “4-level” claim is dropped and the study reports a 3-level contrast.

### 4.2 Outcome labels

- **Sample:** 160 responses, stratified 3 models × 4 levels (≈13/cell), 2 raters (two blind co-authors), blind to arm.
- **Labels:** same / silent propagation / flagged / action switch / other; adjudicate disagreements.
- **Pass criteria:** Cohen’s κ ≥ .80 for the 4-way label; PPV of automatic “silent propagation” ≥ .90; parser failure <1%; 10% seed-repeat label stability ≥ .90.
- **Failure:** if κ < .70 after one rubric revision, downgrade to action-level Y^A only; the content-sensitivity claim is not made. If .70 ≤ κ < .80, report both automatic and adjudicated analyses with the adjudicated result as primary on the audit subsample.

### 4.3 Recovery labels

- 80 recovery sequences (40 R1, 40 R2/censored), 2 raters; 3-way label (corrected / flagged / still wrong); κ ≥ .75. Disagreements adjudicated; the automatic signature match is the primary, human labels validate it.

### 4.4 Annotation and blinding

Two blind co-authors handle the 160 outcome items + 40 severity items (≈200 judgments × 2.5 min = 8.3 h of team time). There is no cash annotation cost. All raters are blind to arm labels; the second rater sees only the response and the rubric, not the perturbation record. A third co-author adjudicates disagreements.

---

## 5. Power and MDE

### 5.1 Assumptions

Monte Carlo (numpy; 600–800 replications/cell), conditional logistic regression with four levels, linear dose, random prefix intercept σ=1.0, baseline placebo switch p0=4%, balanced design, α=.05 two-sided, 80% power. The MDE is the smallest true slope with ≥80% rejection. At p0=5% the MDE improves by ~10%; at p0=3% it worsens by ~10%. Task/step stratification adds ≤10%. The pilot’s 0/60 placebo jitter is treated as an optimistic floor, not an estimate; the design assumes 4% and gates at 20%.

### 5.2 Dose-response slope

| Prefixes per model | MDE b (logit/level) | OR per level |
|---:|---:|---:|
| 400 | 0.280 | 1.32 |
| 500 | 0.259 | 1.30 |
| 600 | 0.234 | 1.26 |
| **800** | **0.203** | **1.22** |

- Per-model SE at b=0.5, N=800: **0.074**; 95% CI ±0.15.
- **Between-model slope difference:** MDE ≈ **0.29 logit** (independent; conservative because models share prefixes). A 2× slope difference (e.g. 0.4 vs 0.8) is detectable; a 0.2 difference is not.
- **Task-specific slopes** (N=400/task): MDE ≈ 0.28; a task × severity difference of ~0.4 is detectable, ~0.2 is not.
- **Pooled across three models:** MDE ≤0.20 conservative, expected 0.14–0.17.
- **Pilot-implied b≈0.8:** >99% power at N=800. The slope is not the risk; the construct validity of Y is.

### 5.3 Recovery half-life

H=4, discrete exponential, t½=2 steps (75% events by H=4), per model N=800:

| s=3 switch rate q | Switchers/model | 95% CI on t½ | Between-model MDE ratio |
|---:|---:|---:|---:|
| 15% | 120 | ±22.5% | 46% |
| 20% | 160 | ±19.8% | 40% |
| 30% | 240 | ±15.7% | 32% |

- At t½=3 (60% events): CI ±21–24% per model (q=15–20%), ±17% at q=30%; between-model MDE 33–49%.
- **Pooled across three models** at q=20%: n=480, 95% CI **±11.4%**; at q=30%: ±9.2%. Pooled figures assume independent model estimates; shared prefixes make them approximate planning targets, not guarantees.
- **Between-severity t½ contrast** (s=2 vs s=3): MDE ≈ **31–50%** depending on the two switch rates; only large differences are detectable.
- If q<10% (fewer than 80 switchers/model), the per-model t½ CI approaches or exceeds ±28%; the half-life is exploratory, not a confirmatory claim.

**Interpretation:** the study is powered to detect a graded slope and to estimate a pooled t½ with ±9–12% precision. It is **not** powered to rank models on t½, to detect small model × severity interactions, or to resolve adjacent-severity recovery differences. Those are explicitly exploratory.

### 5.4 Power for the key secondary contrast

s=3 vs s=1 on Y, paired McNemar: with p1=5%, p3=20%, discordance ≈20%, N=800 gives an MDE of ≈4.4 pp; the expected 15 pp gap is far above it. If the true gap is only 5 pp, the study is marginal; the slope test is the more efficient primary.

---

## 6. Compute and cash

### 6.1 Generation budget

| Component | Generations |
|---|---:|
| Base: 800 × 5 arms × 3 models | 12,000 |
| Recovery transient: 800 × 2 severities × 4 steps × 3 models | 19,200 |
| Recovery persistent: 200 × 1 severity × 4 steps × 3 models | 2,400 |
| Jitter repeats: 80 × 5 arms × 3 models | 1,200 |
| **Total** | **34,800** |

Average prompt ≈2,500 tokens (system policy + history + observation); output ≈150 tokens. ≈87M prefill + 5.2M decode.

### 6.2 Rental and runtime

- 24GB RTX 3090/4090 community instance, AWQ 4-bit, vLLM, batch 8–16, 8–12k context.
- Estimated 35–45 GPU-hours (prefill+decode+1.5× overhead). At $0.30–0.50/h: **$12–22**.
- If only a $0.89/h secure 4090 is available, cap at 30 GPU-hours ($26.7) and reduce N to 600 or drop the persistent arm; still ≥500 prefixes. The hard cap is $70, not a target.

### 6.3 Cash

| Item | Spec | Cost |
|---|---|---:|
| GPU rental | 35–45 h, 24GB, $0.30–0.50/h | $15–22 |
| Storage/egress | 60GB weights + logs | $2–3 |
| Annotation | 200 judgments, team members | $0 |
| Contingency | GPU/storage overrun | $3–5 |
| **Total** | base assumes $18 GPU, $2 storage, $3 contingency | **$23 base / $70 cap** |

No paid API, no second GPU, no HF download in this CPU gate. The rental downloads the three open-weight models once; storage/bandwidth is included.

---

## 7. Pre-registration, gates, and stop conditions

### 7.1 Fixed sequence

1. G0 (CPU, 2 days): harvest ≥880 eligible, with ≥440 per domain before audit and ≥400 per domain after audit; 40-item audit ≥90%; replay match ≥98%; freeze.
2. G1 (GPU, ~3 h): 60-prefix calibration per model. Pass: parse ≥95%; placebo jitter ≤20%; s=3 switch rate 10–80%; no model refuses the task format.
3. G2 (GPU, main): full run; stop if spend reaches $22 or 45 GPU-hours before completion; analyze what is finished.
4. G3 (analysis): slope replicated in ≥2/3 models with the same sign; pooled β>0; label κ gates passed.
5. G4 (recovery): ≥50% of the s=3 risk set recovers by H=4 and exponential is not rejected; otherwise report RMST/curve, no t½ law.

### 7.2 Stop / NO-GO conditions

- **G0 fail:** <880 eligible before audit, <440 per domain before audit, <400 per domain after audit, or replay match <98% → NO-GO for 800; fallback 500 prefixes/2 models (still ≥500) or stop.
- **G1 fail:** parse <95%, placebo jitter >20%, s=3 switch <10% (insensitive) or >80% (ceiling) → stop and fix; do not spend the main budget.
- **G2 fail:** budget/runtime cap reached → stop; no second rental.
- **G3 fail:** pooled slope ≤0, or positive in fewer than 2/3 models, or opposite task slopes → no law claim; report a null/measurement paper.
- **G4 fail:** <50% recovery events or Weibull shape ≠1 → no t½ claim; report the recovery curve/RMST.
- **Label fail:** κ<.70 after revision → action-level only; no content-sensitivity claim.
- **Construct fail:** s=1 (non-critical) moves decisions as much as s=2 → severity labels invalid; collapse levels and re-audit.

### 7.3 What would falsify the claim

A flat slope (β≤0.1) in all models; a negative slope; placebo jitter >20%; s=3 switch >80% (everything flags); model slopes with opposite signs; no recovery by H=4; or a task × severity interaction that reverses the sign in one domain. Any of these converts the paper from “law” to “measurement/null,” and the report must say so.

---

## 8. Threats to validity

1. **τ-bench contamination/outdated tasks.** Public tasks may be memorized; the perturbation is novel, but baseline reliance may be inflated. Mitigation: verbatim accuracy, s=0 jitter, optional template-transfer arm; report domain scope.
2. **Mock tool outputs vs live APIs.** τ-bench outputs are real benchmark tool outputs, not live web/API responses. This is the price of deterministic replay and recovery; external validity to live tools is a follow-up.
3. **Donor-prefix off-policy.** Target models continue gpt-4o/sonnet histories. The within-prefix contrast is valid; donor style may affect external validity. Stratify and report donor-specific slopes.
4. **Severity construct validity.** The s=1 negative control, 3-rater audit, and s=3 plausibility check are the safeguards. If they fail, the “dose” is not a dose.
5. **Outcome label noise.** Deterministic slot matching + 160-item double-coding + 10% seed repeats. If κ is low, downgrade to action-level.
6. **Recovery model.** Exponential is a strong assumption; Weibull shape test and RMST are pre-registered. Transient vs persistent separates re-query from correction.
7. **Model coverage.** Three 7–8B families, not frontier models; the law may not transfer. State the scope explicitly.
8. **Annotation independence.** Two blind co-authors; the second sees only the response and the rubric. No external rater.
9. **Multiple comparisons.** Fixed-sequence gate; heterogeneity exploratory; no post-hoc model ranking.

---

## 9. Path

| Step | Time | Output |
|---|---|---|
| Harvest, replay harness, perturbation generator, pre-registration | 2 CPU days | frozen 800-prefix manifest; G0 report |
| Calibration (60 prefixes × 3 models) | 3 GPU hours | G1 pass/fail; prompt/grammar fixes |
| Main run (34.8k generations) | 35–45 GPU hours | raw continuations, recovery traces |
| Labeling + human audit + analysis | 5–7 CPU days | κ/α report; slope and t½ estimates |
| Write-up + dataset release | 5–7 days | CTO-800 dataset; paper draft |

**Deliverables:** (i) CTO-800, a fixed-prefix counterfactual observation dataset with perturbation records, real outputs, continuations, and recovery traces; (ii) a confirmatory paper on content sensitivity and recovery, with a pre-registered analysis. If the slope is null, the dataset and the null are still publishable; if the slope replicates, the paper’s claim is the graded content-sensitivity law, not a universal constant.

**Study elapsed:** 2–3 weeks full-time; **cash $23 base, $70 cap**.

---

## 10. Final decision (≤300 words)

**GO, staged, hard cap $70; NO-GO to a universal law or model-ranking claim.**

**Design.** 800 real τ-bench prefixes (400 airline, 400 retail; from 1,980 public donor trajectories), 3 open-weight 7–8B models (Qwen2.5-7B, Mistral-7B-v0.3, Llama-3.1-8B), 5 arms: verbatim JSON anchor, faithful prose s=0, non-critical s=1, critical s=2, critical+contradiction s=3. Same prose format across s=0…3; paired within prefix; 4-step recovery; transient primary, persistent 25%. Primary estimand: conditional-logistic slope b_m of content switch on severity; secondary silent propagation; recovery t½ from a discrete exponential.

**MDE.** N=800 gives 0.20 logit/level per model (OR 1.22), 80% power; pooled ≤0.15; pilot-implied b≈0.8 gives >99% power. Recovery with 160–240 s=3 switchers: per-model t½ 95% CI ±16–20%, pooled ±9–11%; between-model/severity differences need 32–50%, so no ranking claim.

**Cost.** 35–45 GPU-hours on a 24GB rental ($15–22), $2–3 storage, $0 annotation (team), $23 base/$70 cap; no paid API.

**Gates.** G0 harvest ≥880 eligible, ≥440/domain before audit and ≥400/domain after audit, 40-item audit ≥90%; G1 parse ≥95%, jitter ≤20%, s=3 switch 10–80%; G2 stop at $22/45h; G3 slope replicated in ≥2/3 models; G4 ≥50% recovery events and exponential not rejected.

**Path.** 2 CPU days harvest/freeze; 3 GPU hours calibration; 35–45 GPU hours main run; 1 week labeling/analysis; 2–3 weeks total.

**NO-GO** if G0/G1 fail (fallback 500 prefixes/2 models or stop), if the slope is flat/negative or not replicated, or if recovery is unidentifiable. The harvest supply is empirically demonstrated (6,554 value-used candidates across 165 task–domain pairs), so the binding risk is construct validity, not prefix supply.
