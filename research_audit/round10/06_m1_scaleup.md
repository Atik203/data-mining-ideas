# 06 — M1 scale-up gate: empirical transfer paper, not η theory

**Date:** 8 November 2026.
**Inputs:** `round9/pilot_idea4.md`, `round9/pilot_idea4/SPOTCHECK_REPORT.md`, `pilot_idea4/results_i04.csv`, `analyze_i04.py`, `run_pilot_i04.py`; `round8/02_multilingual_scan.md` §M1; `round9/FINAL_SURVIVORS_REVIEW.md` §M1.
**Constraint:** CPU only; no pilot re-run; no edits to other agents' files.

---

## 0. Verdict

**M1 proceeds as an empirical measurement paper.** The η theorem is dropped from the claim set, not deferred as "future work" in the abstract. The 9k/7B study delivers three pre-registered quantities: (i) the native→romanized gap at 7B, (ii) whether paired contrastive alignment closes any of it against a shuffled-pair placebo, and (iii) whether the gap scales with the *amount of transliteration loss* — a manipulated factor, not a bound term.

η survives only as a **manipulation**: three romanization variants (lossless / current / lossy) identify the information-loss component directly. That converts the failed theory into a measurement, and the measurement is logically prior to any theorem: a bound whose η term predicts a flat variant curve would be false.

---

## 1. Why the η theory is not needed (and is not testable here)

The Round-8 claim was

> R_tgt(h) ≤ R̂_src(h) + d_HΔH(D_src, D_tgt | script) + d_script(native‖romanized; η) + λ* + O(√((d+log 1/δ)/n)),
> with a matching minimax lower bound Ω(η + n_src^{−β} + n_tgt^{−α}) and a paired-triplet sample-size reduction factor (1−ρ_script).

Four problems, in increasing order of severity:

1. **`d_script(·; η)` is undefined and unestimated.** No operational definition of η appears in the pilot, the corpus, or the analysis. A residual fitted after the fact is not a measurement, and a bound cannot be validated by a residual.
2. **The bound constrains a hypothesis class, not the model we measure.** Native and romanized inputs are deterministic transforms of the *same* items with the *same* labels, so a class containing a script-invariant predictor has d_script = 0 by construction — yet the 7B model actually trained can still fail on romanized inputs. The bound therefore cannot predict the quantity the experiment estimates.
3. **The "matching" lower bound is definitional.** If a fraction η of items are not label-identifiable from the romanized input, no estimator can beat the Bayes floor of (1−η)·acc_native. Stating Ω(η) is restating the Bayes risk; it is not a theorem-level contribution and reviewers will say so.
4. **The sample-size factor (1−ρ_script) is standard paired-design variance reduction.** True, useful, and already well known. It does not need a new bound.

Nothing in the pilot can distinguish this bound from the trivial version: there is no source-risk term, no unpaired arm, no η estimate, and no hypothesis class whose script-invariance is measured. **A theory paper built on it would be rejected on the theory, and the empirical result would be lost with it.**

What would revive a theory (not this round): define η as the fraction of label-relevant tokens whose romanization is non-invertible, prove a bound in terms of the *model class's* label-recovery rate from the romanized input, and validate the predicted slope across the variant ladder. That is a separate paper and it needs the measurement below first. **Defer the theorem; do not claim it.**

---

## 2. What the pilot actually established

The saved results understate their own uncertainty. Re-reading `results_i04.csv`:

| Check | Pilot report | What the file shows |
|---|---|---|
| Test sample | 110 cells/arm, "grouped 150/50" | 89 unique items, 7 source groups total (sizes 7–21), **4 groups per seed** |
| Test set fixed across seeds? | implied yes | **No.** `group_split()` re-shuffles per seed: seed 42 tests g00/g03/g04/g11 (46 items); seed 1234 tests g00/g01/g14/g15 (64 items); only **21 items overlap** |
| Native→roman gap | 11.82 pp, t=1.96, p=.052 | seed 42 **19.57 pp** (n=46); seed 1234 **6.25 pp** (n=64); cluster-bootstrap 95% CI **[0.35, 25.06] pp** |
| Alignment gain | 6.36 pp, t=1.54, p=.127 | seed 42 **2.17 pp**; seed 1234 **9.38 pp** |
| MDE (80%, one-sided) | not reported | **≈22–32 pp** at n_eff 32–69 after clustering |
| Romanization validity | 30/30 AI spot check | mechanical conformance only; no native speaker, no pronunciation claim |
| InfoNCE objective | τ=.07, λ=.1 | batch of **2**: each anchor's only negative is itself — an attract-only loss, not contrastive |

Two design faults must be fixed before any 7B spend:

- **The test set is not fixed across seeds and is confounded with group.** Holding out 4 whole source groups (7–21 items each) means the gap is partly a group-difficulty difference. The scale-up must split by **source document**, stratify the split by group so every group appears in train and test, and freeze one split for all runs.
- **The alignment objective was under-specified.** In-batch negatives and a projection head are required; otherwise "alignment" is a two-view regularizer with no discriminative term.

**Bottom line:** the pilot establishes *direction* (native > roman; B > A) and nothing about magnitude. Its 11.8 pp point estimate is compatible with 0 and with 25 pp. The confirmation must be powered on the possibility that the 7B gap is much smaller.

---

## 3. Estimands (pre-registered)

Let Y_i(s; θ) ∈ {0,1} be correctness on item i under rendering s and trained model θ.

1. **Script gap** (primary): Δ_script = Σ_t w_t · E_i∈t[Y_i(native) − Y_i(R1)], with fixed task weights w_t = 1/3. Identified within item: the only change is orthography; label and content are fixed. Valid only if the R1 rendering is meaning-preserving (Gate G0).
2. **Alignment gain** (primary): Δ_align = E_seed[ E_i(Y_i(R1; B_seed) − Y_i(R1; A_seed)) ]. Identified by randomized arm assignment with seed matching; estimand is intention-to-treat over training runs.
3. **Placebo contrast**: Δ_placebo = E[Y(R1; P) − Y(R1; A)]. A method claim requires Δ_align > 0 **and** Δ_placebo ≈ 0.
4. **Loss effect** (mechanism): β_η = E_i[Y_i(R2) − Y_i(R3)], where R2 is lossless/reversible and R3 is lossy social-media style. Native cancels, so this is a clean paired contrast with no training variance. Prediction under the η story: β_η > 0; under a pure script/tokenizer story: β_η ≈ 0.
5. **Validity**: P(label-changing ambiguity) in the native audit, with a Wilson 95% CI.

The gap is a causal effect of *this rendering*, not of "script in general". Tokenizer coverage is bundled in and is not separately identified; the lossless variant is the closest available control and must be reported as such.

---

## 4. Design

### 4.1 Corpus: M1-Bangla-Paired-9k

| Task | Items | Source | Label |
|---|---:|---|---|
| NLI | 3,000 | IndicXNLI-Bengali dev/test + fresh human-written items to reach 3,000 | 3-way |
| Sentiment | 3,000 | public Bangla SA + 500 fresh | 2-way |
| MCQ-QA | 3,000 | Bangla QA converted to 4-way MCQ (one correct span, three distractors matched on type/length) | 4-way |

Every item exists in native and **three romanizations**:

- **R1** — the current deterministic scheme (`fix_roman.py`), unchanged.
- **R2** — lossless/reversible transliteration: explicit inherent-vowel and hasanta markers, nukta preserved, unambiguous back-mapping. Ugly but information-preserving.
- **R3** — lossy informal romanization: aa→a, bh→b, chh→ch, sh→s, final vowel deleted, gemination collapsed. Mimics social-media spelling.

Corpus filters: both scripts tokenize to ≤480 tokens at the 512 budget (prevents differential truncation); digit/acronym preservation; ASCII-only in roman columns; idempotence of the romanizer; length ratio recorded. Items failing any filter are fixed or excluded before the split, and exclusions are reported.

**Split:** one frozen, document-clustered, group-stratified 50/10/40 split → 4,500 train / 900 dev / 3,600 test (≈1,200–2,000 test clusters, conservatively assuming 3 items/cluster). The split is generated once, hashed, and stored. All seeds and arms use it. No re-shuffling.

### 4.2 Arms, model, runs

Base: **Qwen2.5-7B-Instruct**, 4-bit NF4, LoRA r16/α32 on q,k,v,o (r8 in the pilot; r16 here to avoid capacity-limited underfit at 7B — report as a design change), dropout .05, lr 2e-4, batch 1 × accum 8, **3 fixed epochs, no early stopping** (removes a researcher degree of freedom). Max len 512.

| Arm | Loss | Purpose |
|---|---|---|
| A | CE on native + R1 | baseline |
| B | CE + λ·InfoNCE, λ=.1 | paired alignment |
| P | CE + λ·InfoNCE with shuffled pairs | placebo (extra loss term, wrong pairing) |
| B.3 | CE + λ·InfoNCE, λ=.3 | dose (conditional) |

InfoNCE fix: final-token hidden state → projection head (d→256→128, discarded at inference); **in-batch negatives with 8 pairs/batch**; τ=.07.

Runs: 4 seed-matched seeds (42, 1234, 2024, 3407) × {A, B, P} = **12 core runs**; +4 B.3 runs conditional on B−A ≥ 2 pp at interim. Size curve: A only at 500 and 1,500 train items, 2 seeds each = **4 runs**. Generality: Llama-3.1-8B, A/B, 2 seeds = **4 runs** (priority 3). Total **20 planned, 24 worst case**.

GPU: 7B 4-bit LoRA, 4,500 items × 2 scripts × 3 epochs = 27k steps/run at ~0.25–0.4 s → 2–3 h/run. Core+curve ≈ 32–48 GPU-h; +B.3 +Llama ≈ 52–76 GPU-h. At $0.30–0.45/h → **$16–34**. Fits the 24GB / $70 envelope with margin for one rerun.

---

## 5. Power and MDE

All one-sided α=.025. **Primary test:** document-clustered bootstrap (10,000 reps) of the task-stratified contrast, with a cluster-robust Wald/permutation p-value; exact McNemar on the item-level discordant counts is the secondary check and the basis for the MDE table below. Gain uses the seed-level paired t (df=S−1) because between-seed variance is real and the item-level SE alone understates it.

**Script gap** (n_test = 3,600; design effect 1.25–1.35 from document clustering → n_eff ≈ 2,670–2,880):

| discordance π_d | SE (pp) | MDE80 | MDE90 |
|---:|---:|---:|---:|
| .20 | 0.85 | 2.4 | 2.8 |
| .30 | 1.04 | 2.9 | 3.4 |
| .40 | 1.20 | 3.4 | 3.9 |

So: **MDE90 = 3.4–3.9 pp**, well below the pilot's 11.8 pp and below any gap the literature would call a phenomenon. Precision: 95% CI half-width ≈ 2.0–2.4 pp. A true 2 pp gap gives only ~50% power; a null result must therefore be reported as **inconclusive**, not as evidence of no gap, unless the CI upper bound is below 2 pp.

**Alignment gain** (n_eff ≈ 2,800; 4 seeds; between-seed SD of the per-seed gain 1.5–2.0 pp):

| σ_seed | SE_seed (pp) | MDE90 |
|---:|---:|---:|
| 1.5 | 0.75 | 3.6 |
| 2.0 | 1.00 | 4.8 |

**MDE90 = 3.6–4.8 pp.** A pilot-sized 6.4 pp gain is comfortably detectable; a 4 pp gain is marginal; a 2–3 pp gain will be **inconclusive, not negative**. State this in the paper. Six seeds would bring MDE90 to 2.5–3.3 pp but adds ~20 GPU-h; run 6 seeds only if the interim σ_seed > 2 pp (the pre-registered rule).

**Secondary endpoints**

| Endpoint | n | MDE | Status |
|---|---:|---|---|
| Per-task gap | 1,200/task | ~6 pp (90%) | descriptive only; do not claim task differences below 6 pp |
| R1 vs R2 vs R3 (β_η) | 2,000, paired | ~3 pp (80%) | confirmatory mechanism, no extra training |
| Human-written vs R1 gap | 500 | ~7 pp (80%) | gross-failure check only; 3–5 pp discrepancies are reported but not gated |
| Native audit ambiguity | 900 | ±1.4 pp (Wilson 95%) | validity gate |

**Why the pilot could not do this:** its honest MDE80 is 22–32 pp. The 11.8 pp estimate is inside its own noise band; p=.052 is not "borderline significant", it is "uninformative about magnitude".

---

## 6. Native-review protocol

The 30/30 AI check certifies only that `fix_roman.py` is deterministic and scheme-faithful. It explicitly does **not** certify pronunciation, word choice, or fitness to publish. It cannot be cited as native validation.

**Stage A — machine audit, 100% (27,000 cells).** Conformance, ASCII-only, idempotence, digit/acronym preservation, length ratio ∈ [0.8, 1.6], no double spaces, no truncation at 480 tokens. Any failure blocks the item.

**Stage B — stratified human review, 900 items (10%), 2 annotators.** Strata: task (3) × romanization difficulty (routine / nukta-chandrabindu-conjunct / proper-noun-acronym) = 9 cells × 100. Annotators are native Bangla speakers (one Bangladesh, one West Bengal), independent, blind to model results. Ratings: meaning preservation (1–5), readability (1–5), ambiguity (yes/no + alternative reading), label-changing ambiguity (yes/no). Krippendorff's α on the ordinal scales; adjudication round if α < .67.

**Stage C — back-transliteration, 200 items.** Roman → native, blind to source; word-level exact match target ≥90%.

**Stage D — variant validation, 200 items.** R1/R2/R3 rated for ambiguity and naturalness. Required: R2 single-reading ≥98%; R3 naturalness ≥4/5. If R2 is still ambiguous, the "lossless" construct failed and β_η is uninterpretable.

**Stage E — human-written romanizations, 500 items, 2 writers.** Social-media style, written independently from native. Each back-transliterated by the other writer; disagreements adjudicated. This is the ecological-validity check for R1.

**Acceptance:** ≥95% of sampled items rated meaning ≥4/5 by both annotators; label-changing ambiguity ≤2%; α ≥ .67; back-transliteration ≥90%. Failures trigger one revision round; a second failure is Gate G0.

**Cost:** 900×2×$0.10 = $180; 500 human romanizations × $0.15 = $75; 500 back-transliterations × $0.10 = $50; adjudication ≈ $15 → **≈$320**. Minimum tier (300-item audit, 200 human romanizations) ≈ $70–90. The compute budget does not cover this; it is the real gate. If it cannot be funded, frame the paper as "machine-audited corpus" and submit to a venue that accepts that — do not claim native validation.

---

## 7. Stop conditions

| Gate | Trigger | Action |
|---|---|---|
| **G0 corpus** | Native review fails acceptance after one revision (>2% label-changing ambiguity, or <95% meaning ≥4) | **STOP.** No GPU. Kill M1; reassign to M3. |
| **G1 futility** | Interim (2 seeds at 4,500): gap point estimate <2 pp | **STOP the method program.** Publish "scale closes the script gap" as a short paper; do not run alignment arms. |
| **G2 degeneracy** | Dev native accuracy >92% or <45% at 4,500 | **STOP evaluation.** Rebalance difficulty (add hard items / reduce epochs); test numbers from a degenerate operating point are uninterpretable. |
| **G3 alignment** | B−A ≤0, or placebo P−A ≥ 0.5·(B−A) | **STOP the method claim.** Publish gap + failed alignment + placebo. Do not run B.3 or the 6th seed. |
| **G4 base** | Zero-shot 7B (no LoRA) gap <1 pp | **STOP.** The phenomenon is a small-model artifact; publish the scale result. |
| **G5 budget** | 80 GPU-h or $70 reached | Drop in priority order: Llama → B.3 → 6th seed. Never drop P (placebo) or the 4th seed. |
| **G6 construct** | Human-written romanization gap differs from R1 by >5 pp or reverses sign | Demote R1 to a control; make human-written the primary rendering, or report the construct failure. |

The interim look is used only for **variance components** (π_d, ICC, σ_seed) to lock N; it is not used to choose the hypothesis. The futility stop in G1 is a program decision, not a hypothesis test, and is reported as exploratory.

---

## 8. Path, timeline, budget

1. **Now, CPU ($0):** build the 9k corpus; run the machine audit; write and hash the pre-registration (estimands, split, arms, analysis code, decision tree).
2. **Native review (~$320, 2–3 weeks):** Gate G0. **Do not rent a GPU before this closes.**
3. **Interim (6 runs: size curve at 500/1,500 + 2 seeds of A at 4,500, ~5 h, ~$2):** operating point, G2, G4, G1 futility; lock π_d, ICC, σ_seed.
4. **Confirmation (10 remaining core runs + 4 conditional dose, ~25–40 GPU-h, ~$12–18):** primary endpoints.
5. **Mechanism (inference only, ~2 h):** R1/R2/R3 on 2,000 items + human-written subset.
6. **Analysis and paper.** Total ≈5–7 weeks, ≈$340–355 cash (annotation dominant), 52–76 GPU-h.

**Publication decision tree**

| Outcome | Paper |
|---|---|
| Gap ≥3.5 pp, B−A significant (CI excludes 0), placebo null | ACL/EMNLP main or Findings: paired script-gap measurement + method |
| Gap ≥3.5 pp, B−A ≈0 or placebo matches | Findings/workshop: the gap is real, paired alignment does not fix it |
| Gap <2 pp and CI upper bound <2 pp | Short paper: scale closes the gap; kill the method line |
| Gap <2 pp but CI reaches 2 pp | Inconclusive: report the estimate, claim neither replication nor closure |
| G0 fails | No paper from M1; release the audit as a negative resource note |

---

## 9. What a reviewer will attack

1. **"This is a dataset plus a LoRA ablation."** Defense: pre-registered within-item contrast, fixed split, placebo, dose, variant ladder, two model families, and a scale comparison (1.5B → 7B). The contribution is the decomposition, not the adapter.
2. **"Your romanization is artificial."** Defense: R1 vs human-written gap (Stage E) and R3 (social-media style). If they diverge, we report it.
3. **"The gap is tokenizer coverage, not script."** Conceded as a limitation. R2 is the best available control; the paper must say the tokenizer component is not separately identified.
4. **"XNLI test items are contaminated."** Defense: fresh 500-item human-written subset per task, memorization probe on a sample, and a public-vs-fresh gap comparison. If they differ by >5 pp, the public estimate is demoted (the 500-item subset only has ~7 pp MDE, so smaller discrepancies are reported, not gated).
5. **"Where is the theory?"** Answer: removed deliberately. The measurement is the precondition for a theory, and the variant ladder is the test any future bound must pass.

---

## 10. Final summary (≤300 words)

**Theory vs empirical.** Empirical. The η bound is untestable here — no independent η, no source-risk term, no unpaired arm — and its "matching" lower bound Ω(η) is the Bayes noise floor, i.e. definitional. η survives only as a manipulated factor.

**Design.** 9,000 Bangla items (3,000 NLI, 3,000 sentiment, 3,000 MCQ-QA), each native plus three romanizations: R1 current scheme, R2 lossless, R3 lossy social-media. One frozen document-clustered, group-stratified 50/10/40 split (4,500/900/3,600; ~1,200–2,000 test clusters). Qwen2.5-7B 4-bit LoRA, three fixed epochs. Size curve at 500/1,500/4,500 (arm A, 2 seeds). At 4,500: A (CE), B (CE + in-batch paired InfoNCE, λ=.1, projection head), P (shuffled-pair placebo), B.3 (λ=.3), four seed-matched runs each. Inference-only R1/R2/R3 on 2,000 items; Llama-3.1-8B A/B, 2 seeds.

**Power.** With 3,600 test items (design effect 1.25–1.35, n_eff≈2,800) the gap has MDE90 = 3.4–3.9 pp. The alignment gain (4 seeds, between-seed SD 1.5–2.0 pp) has MDE90 = 3.6–4.8 pp. The R1/R2/R3 contrast has ~3 pp MDE; per-task gaps are descriptive (MDE ~6 pp). The pilot's honest gap CI is [0.4, 25] pp — it cannot estimate magnitude.

**Stop.** G0 native review fails (>2% label-changing ambiguity) → no GPU. G1 interim gap <2 pp → publish scale-closure, stop method. G2 dev accuracy outside 45–92% → fix difficulty. G3 B−A ≤0 with placebo matching → stop alignment. G4 zero-shot 7B gap <1 pp → small-model artifact. Hard cap 80 GPU-h / $70.

**Path.** Corpus + native audit (~$320) → 5 h interim (6 runs) → 10-run confirmation (~$15) → mechanism inference → paper.
