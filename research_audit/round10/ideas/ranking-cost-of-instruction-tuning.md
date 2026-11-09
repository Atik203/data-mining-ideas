# The Ranking Cost of Instruction Tuning: Quality Beyond Accuracy and Calibration

- **Internal short name:** RANKCAL
- **Status:** #2 ranked data-mining idea · GO, conditional · not started
- **Score:** Core **6.75** / Weighted **7.18** · **Independence 8.5/10**
- **Date:** 2026-11-09
- **Audience:** project supervisor and the data-mining team
- **Catalog:** `Atik203/data-mining-ideas`, `research_audit/round10/ideas/`

> **Catalog note.** E2 — Tool-Observation Sensitivity in Language-Model Agents — is the **selected #1 data-mining project**. RANKCAL is the **#2 ranked idea**, kept as an **independent research option and fallback for team discussion**. It is **not part of the E2 project** and is **not the selected data-mining project**. This page lives in the `data-mining-ideas` catalog because that is where the idea pages are maintained; it is not an E2 deliverable.

> **Evidence status.** The only empirical evidence is a 500-item pilot on a 1.5B model pair. It is a scale-up signal, not a confirmed effect. The confirmatory 7B run has not been executed. No number below is a confirmatory result.

---

## 1. One-page summary for the supervisor

**Problem.** Accuracy says how often the model is right. Calibration says whether its confidence matches its hit rate. Ranking says whether correct answers get higher confidence than wrong ones. Deployed systems use all three: abstain, escalate, route, or queue by confidence. A model can gain accuracy and lose ranking.

**Idea.** Compare a base model and its Instruct sibling on the same items. Measure accuracy, AUROC (ranking), AURC (selective prediction), and ECE (calibration). Test a pre-registered 0.03 margin for a tax or equivalence.

**Pilot.** 500 MMLU items, Qwen2.5-1.5B. Accuracy 0.512 → 0.544 (+0.032); AUROC 0.7652 → 0.7259 (-0.0393), 95% CI [-0.0821, +0.0034]; ECE 0.1333 → 0.1818 (+0.0485, the only significant change); isotonic ECE 0.069 → 0.062, closing 84% of the ECE gap. MDE at N=500 was 0.061, so this is a scale-up signal, not a confirmed tax.

**Confirmatory design.** 2,500 MMLU test items, seed 0; Qwen2.5-7B base and Instruct primary; Llama-3.1-8B base and Instruct replication; 4-bit, T=0, fixed completion prompt; 500-item chat-template arm; ARC-Challenge and GSM8K secondary. Item MDE 0.028, cluster-robust 0.036; paired DeLong; TOST at ±0.03 item-level (±0.04 cluster-robust).

**Cost and timeline.** About 9,000 generations, 4–8 GPU-hours on one 24GB card, under $30; 4–5 months part-time.

**Decision requested.** Keep RANKCAL as the #2 fallback and do not start it while E2 is active. If E2 fails a gate or is descoped, approve the 200-item calibration and the 2,500-item run, and confirm that the two studies will not run concurrently.

---

## 2. The problem in plain language

A model answering a four-option question produces a letter and four probabilities. Three questions follow. **Accuracy:** was the letter right? **Calibration:** when the model says 0.80, is it right about 80% of the time? **Ranking:** do correct answers get higher confidence than wrong answers? **Confidence** is the probability assigned to the chosen answer, not a statement of truth.

Two models take eight questions; both get six right (accuracy 0.75).

| Item | Model A confidence | A correct? | Model B confidence | B correct? |
|---|---:|---|---:|---|
| Q1 | 0.90 | yes | 0.90 | yes |
| Q2 | 0.85 | yes | 0.70 | no |
| Q3 | 0.80 | yes | 0.60 | yes |
| Q4 | 0.75 | yes | 0.55 | yes |
| Q5 | 0.70 | yes | 0.50 | yes |
| Q6 | 0.65 | yes | 0.45 | yes |
| Q7 | 0.60 | no | 0.40 | yes |
| Q8 | 0.55 | no | 0.35 | no |

Model A orders every correct answer above every wrong one; Model B puts a wrong answer at 0.70 above five correct ones. Same accuracy, different ranking.

**AUROC.** Probability that a random correct item outranks a random wrong one, ties half. 0.5 is chance; 1.0 is perfect. Model A is 1.00; Model B is 0.583.

**AURC.** Sort by confidence, answer the top 10%, then the top 20%, and so on. At each coverage level, measure the error rate among answered items. The area under that risk–coverage curve is AURC; lower is better. It summarizes **selective prediction**: answer when confident, abstain otherwise.

**ECE.** Bin items by stated confidence, compare average confidence with actual accuracy, and take the weighted gap. Lower is better. ECE measures whether the numbers are the right size, not whether they are in the right order.

**Why calibration does not fix ranking.** Isotonic regression is a non-decreasing map: it can merge adjacent scores but cannot reverse two items. AUROC is invariant under strictly increasing transforms, so isotonic can lower ECE while the ranking gap remains. In the pilot, isotonic ECE fell to 0.062 but the AUROC gap remained.

---

## 3. Why this matters

A risk–coverage policy is only as good as the confidence order it sorts by. If instruction tuning raises accuracy but lowers AUROC, the model can have a higher error rate at the same coverage. The same order drives routing and the human review queue. Accuracy-only tables hide this, and calibration-only papers can report a successful ECE fix while the order is worse.

---

## 4. The idea and the research questions

**One sentence.** Instruction tuning often raises accuracy but can lower AUROC: the model answers more questions correctly yet ranks correct answers less reliably.

**RQ1 (superiority).** Does instruction tuning change AUROC, and is the change negative and larger than 0.03?

**RQ2 (equivalence).** Is the change small enough to call negligible, that is, is |ΔAUROC| < 0.03?

**RQ3 (replication, secondary).** Does the sign replicate in a second model family and two other benchmarks? Does it vary across four MMLU groups? Does chat-template formatting change it?

The design can return a meaningful tax, a small real tax, equivalence, a rank gain, or an inconclusive result; all five have a pre-written interpretation (Section 11).

**Scope note.** "Instruction tuning" means the released base-to-Instruct transition, a bundle of supervised tuning plus any preference or safety tuning. Public models do not let us isolate one step; the claim is about the sibling pair, not a single causal operation.

---

## 5. What the pilot actually showed

The pilot used 500 MMLU items, 57 subjects, Qwen2.5-1.5B base and Instruct, 4-bit, a fixed completion prompt, no chat template, and T=0.

| Quantity | Base | Instruct | Change (Instruct - base) |
|---|---:|---:|---:|
| Accuracy | 0.512 | 0.544 | +0.032 |
| AUROC | 0.7652 | 0.7259 | **-0.0393** |
| AURC | — | — | -0.0026 (95% CI -0.0239, +0.0189) |
| ECE | 0.1333 | 0.1818 | +0.0485 (95% CI +0.0159, +0.0817) |
| Isotonic ECE | 0.069 | 0.062 | -0.007; closes 84% of the ECE gap |
| κ_all / κ_Y | — | — | 0.593 / 0.615 |

DeLong SE is 0.0218; the 95% CI for ΔAUROC is **[-0.0821, +0.0034]**. The item-level MDE at N=500 is about 0.061; a delete-one-subject jackknife gives SE 0.0234 (MDE 0.066).

**What it establishes.** The paired pipeline works at a usable operating point (not floor or ceiling). The direction is consistent with a rank tax, and it is not a pure calibration shift: isotonic closes 84% of the ECE gap but cannot reorder items, so the AUROC gap remains.

**What it does not establish.** It does not confirm a tax: the CI covers zero and the MDE (0.061) exceeds the observed effect (0.039); the interval is compatible with -0.08 and +0.003. It does not generalize beyond 1.5B, one benchmark, or the completion format. It does not show a uniform loss: AURC is flat (-0.0026, CI includes zero) while AUROC is worse. It does not test subject heterogeneity: the 57-subject bucket leg had zero testable buckets.

**The 57-subject design error.** 500 items over 57 subjects is about 8.8 per subject. The pilot planned buckets of at least 15 items; the largest subject had 14, so none qualified. This is a design error, not evidence of homogeneity. The fix is four coarse groups, analyzed descriptively; the pooled contrast is the only confirmatory test.

**Power, metrics, and κ.** At N=500 the MDE was 0.061, so 0.039 is inside the noise band; "not significant" is not "no effect." AURC emphasizes the high-confidence region; AUROC weights all pairs, so the metrics can disagree. κ_Y = 0.615 is far from 1, but the implied bound |ΔAUROC| ≤ (1 - κ)/2 = 0.192 is too loose to test 0.03; κ is diagnostic only.

---

## 6. The confirmatory design

| Element | Specification |
|---|---|
| Sample | 2,500 MMLU items, subject-stratified (~44/subject), seed 0; 200-item calibration |
| Primary models | Qwen2.5-7B base + Instruct; 4-bit; T=0; identical completion prompt; no chat template |
| Replication | Llama-3.1-8B base + Instruct, same protocol |
| Confidence | Max softmax over A/B/C/D surface variants, renormalized; raw top-4 logits stored |
| Format control | Instruct + chat template, 500 items; sign of ΔAUROC |
| Primary benchmark | MMLU |
| Secondary benchmarks | ARC-Challenge 1,000; GSM8K 1,000; replication (MDE ≈ .044), not confirmatory |
| Primary test | Paired DeLong; subject-stratified bootstrap 10,000; cluster-robust sensitivity |
| Secondary tests | ΔAURC/ΔECE bootstrap 10,000; 5-fold isotonic; κ_Y diagnostic |
| Equivalence | TOST ±.03 item-level (±.04 cluster-robust); 90% CI |
| Groups | STEM, Humanities, Social Sciences, Other; descriptive guardrail |
| Switch rule | If 7B MMLU accuracy > .85, primary to MMLU-Pro/ARC; MMLU secondary |

**Why these choices.** 7B is the smallest class where MMLU is above floor and below ceiling; the local 6GB 3060 cannot host it. Base versus Instruct controls pretraining, tokenizer, and architecture; it is the cleanest public contrast for the post-training transition, though it is a bundle, not a single-step ablation. The completion prompt is identical because base models have no chat template; an Instruct-only template would confound format with tuning. 4-bit is used for both; T=0, with batch nondeterminism reported.

**Confidence extraction.** For each option, take the maximum over common surface forms (` A`, `A`, `A)`, `(A`), renormalize the four scores to sum to one, and use the maximum as confidence. Correctness is the parsed letter matching gold. Raw option logits are stored so every metric is recomputable.

**Format and switch.** The primary contrast is Instruct-completion versus base-completion. The format arm runs Instruct with its chat template on 500 items; if the sign of ΔAUROC flips, the tax is format-dependent. If 7B MMLU accuracy exceeds 0.85, the pre-registered switch moves the primary to MMLU-Pro or ARC and keeps MMLU secondary; it needs a new manifest and calibration.

---

## 7. Dataset

MMLU is a public 57-subject multiple-choice benchmark with about 14,000 test items. The study samples 2,500, proportional to subject size, averaging about 44 per subject. Exact counts are frozen in the item manifest before any model output.

| Group | Subjects | Planned items at N=2,500 | Planned share |
|---|---:|---:|---:|
| STEM | 19 | ~790 | 31.6% |
| Humanities | 13 | ~635 | 25.4% |
| Social sciences | 12 | ~510 | 20.4% |
| Other / professional | 13 | ~565 | 22.6% |
| **Total** | **57** | **2,500** | **100%** |

The four groups are fixed before analysis. They exist because the pilot's 57-subject leg was untestable, not because the study is powered for subgroup claims. The pooled contrast is the only confirmatory test.

**Secondary benchmarks.** ARC-Challenge (1,000 items) is a science multiple-choice set; GSM8K (1,000 items) is grade-school math word problems. They test whether the sign is benchmark-specific. With 1,000 items, the item-level MDE is about 0.044, so they are replication, not confirmation.

**Licensing and release.** The benchmarks are public. Record each upstream license and version; do not re-license items. Release item IDs, subject/group labels, split, seed, and SHA-256 hashes, not benchmark text, plus prompts, model revisions, raw logits, parsed answers, confidences, and code. No new dataset, no human annotation, no PII, no weights.

**Artifacts.** The item manifest is one row per item with ID, subject, group, split, hash, and seed. The run ledger records model, revision, prompt hash, item ID, output, parse status, latency, and cost. Logits and confidences let an independent reader recompute every metric without a GPU.

---

## 8. Metrics and statistics

| Term | Plain-language meaning | Role |
|---|---|---|
| Accuracy | Fraction of items answered correctly | Descriptive; not the estimand |
| Confidence | Probability the model assigns to its chosen option | Input to all ranking metrics |
| AUROC | Probability a random correct item outranks a random wrong item; 0.5 = chance, 1.0 = perfect | Primary ranking estimand |
| AURC | Area under the risk–coverage curve; lower is better | Selective-prediction summary |
| ECE | Weighted gap between confidence and accuracy across bins; lower is better | Calibration level |
| Isotonic ECE | ECE after a monotone, cross-fitted recalibration | Tests whether a level fix is enough |
| DeLong | Paired test for a difference between two correlated AUROCs | Primary superiority test |
| Bootstrap | Resample items or subjects 10,000 times; percentile CI | CIs for ΔAUROC, ΔAURC, ΔECE |
| Cluster-robust SE | Resample subjects, not items, to respect within-subject correlation | Conservative sensitivity |
| MDE | Smallest true effect detectable with 80% power at α = .05 | Design justification |
| TOST | Two one-sided tests; equivalence if the 90% CI fits inside ±margin | Equivalence test |
| κ_Y | Kendall-style concordance of the base and Instruct confidence orders | Diagnostic only |

**Why these tests.** AUROC is invariant to monotone transforms, so isotonic cannot repair it. AURC is risk–coverage: at each coverage, risk is the error rate among the most confident items. ECE uses fixed bins and the bin-mass-weighted |accuracy - confidence|. Isotonic ECE is fitted on four folds and evaluated on the fifth, rotated five times.

**Paired inference.** DeLong tests ΔAUROC = 0 while accounting for the same items; it ignores subject clustering, so the subject-stratified bootstrap resamples subjects, keeps their items, and recomputes 10,000 times.

**Equivalence and multiplicity.** TOST tests |Δ| ≥ .03 vs |Δ| < .03 with two one-sided .05 tests; the 90% CI must lie inside (-.03, +.03). Superiority is tested first; equivalence only if superiority is not significant. One primary contrast, pooled ΔAUROC; everything else is secondary or descriptive.

---

## 9. Power and sample size

| N | Item SE | Item MDE (80%) | Cluster SE | Cluster MDE (80%) | TOST ±.03 power (item/cluster) | TOST ±.04 power (item/cluster) | Superiority at -0.039 (item/cluster) |
|---:|---:|---:|---:|---:|---:|---:|---:|
| 1,500 | 0.01267 | 0.0355 | 0.01523 | 0.0427 | 0.53 / 0.26 | 0.87 / 0.67 | 0.87 / 0.73 |
| 2,000 | 0.01076 | 0.0301 | 0.01387 | 0.0389 | 0.75 / 0.40 | 0.96 / 0.79 | 0.95 / 0.80 |
| **2,500** | **0.01001** | **0.0280** | **0.01298** | **0.0364** | **0.82 / 0.50** | **0.98 / 0.85** | **0.97 / 0.85** |
| 3,000 | 0.00904 | 0.0253 | 0.01236 | 0.0346 | 0.91 / 0.57 | 1.00 / 0.89 | 0.99 / 0.88 |
| 4,000 | 0.00790 | 0.0221 | 0.01153 | 0.0323 | 0.97 / 0.66 | 1.00 / 0.93 | 1.00 / 0.92 |
| 6,000 | 0.00650 | 0.0182 | 0.01063 | 0.0298 | 1.00 / 0.76 | 1.00 / 0.97 | 1.00 / 0.96 |

TOST power is at true Δ = 0; superiority power is at the pilot point -0.039. α = .05 two-sided for superiority; TOST uses two one-sided .05 tests.

**Why 2,500.** Smallest N with item MDE below 0.03 and item-level TOST ±.03 power 0.82. N=2,000 gives item MDE 0.0301 and power 0.75 item / 0.40 cluster; not enough. N=1,500 gives 0.0355 / 0.0427; the earlier "N ≥ 1,500" plan was too optimistic. The binding risk is the 7B operating point.

**What is underpowered.** Groups have 510–790 items, MDE about 0.056, so they are descriptive. Secondary benchmarks (1,000 items) have MDE about 0.044 and are replication only. The format arm (500 items) has MDE about 0.061 and is a sign check. Cluster-robust TOST at ±.03 has power 0.50 at N=2,500, hence the ±.04 margin (power 0.85).

**The ±.03 versus ±.04 decision.** ±.03 is the smallest meaningful change and the item-level primary. ±.04 is the cluster-robust margin because ±.03 is unaffordable at N=2,500 under clustering. Pre-register both; do not switch after seeing Δ. A journal demanding cluster-robust ±.03 needs N=6,000 (0.76) or N≈7,500 (0.80), a separate budget decision. A true null still has an 18% chance of an inconclusive item-level TOST.

---

## 10. Decision rules

Superiority first. If the 95% CI excludes zero, report the estimate and do not claim equivalence. If it includes zero, test equivalence with TOST.

| 95% CI | 90% CI | Conclusion |
|---|---|---|
| excludes 0, entirely below -0.03 | — | meaningful negative tax confirmed |
| excludes 0, within (-0.03, 0) | — | statistically nonzero but small; report magnitude, do not call meaningful |
| includes 0 | inside (-0.03, +0.03) | equivalence: negligible rank change; publish null/equivalence |
| includes 0 | not inside (-0.03, +0.03) | inconclusive; stop, do not spend more |
| excludes 0, entirely above +0.03 | — | rank gain; report as an opposite-direction finding |

**Group guardrail.** Any group with n ≥ 400 and point estimate ΔAUROC > +0.02 forces withdrawal of "systematic" and a heterogeneity report. A group CI covering zero is not a sign flip. All four groups qualify at N=2,500.

**Stop conditions.** Parse failure > 5% or accuracy outside [.35, .85]: fix and rerun calibration; if still outside, stop; do not interpret. Equivalence: write the null paper. Inconclusive: stop. Confirmed tax: write. Small real tax: measurement result, no practical-significance claim. Guardrail: withdraw "systematic." κ is diagnostic only.

**Publishable null.** 95% CI includes 0 and 90% CI inside ±.03, with adequate power and both intervals reported. That supports "no meaningful rank tax at 7B" for these models and this benchmark, not a general claim. A bare p > .05 is not equivalence.

---

## 11. Expected outcomes

| Scenario | Statistical signature | Working title or claim | Action |
|---|---|---|---|
| Confirmed tax | 95% CI entirely below -0.03 | "The Ranking Cost of Instruction Tuning: Accuracy Gains, Ranking Losses at 7B" | Write; report group heterogeneity |
| Equivalence | 95% CI includes 0; 90% CI inside ±0.03 | "No Meaningful Ranking Cost of Instruction Tuning at 7B: An Equivalence Study" | Write the null/equivalence paper |
| Small real tax | 95% CI excludes 0; estimate in (-0.03, 0) | "A Small but Real Ranking Cost of Instruction Tuning" | Report magnitude; no "meaningful" claim |
| Rank gain | 95% CI entirely above +0.03 | "Instruction Tuning Improves Confidence Ranking" | Report the opposite direction; revise the hypothesis |
| Inconclusive | 95% CI includes 0; 90% CI not inside ±0.03 | No paper claim | Internal memo; stop |

The tax and equivalence outcomes both answer the question the design was built to answer.

---

## 12. Why this is a separate research option

| Question | Answer |
|---|---|
| Why not #1? | E2 is #1 at 7.38/7.55, active, with the rental plan. |
| Why #2? | Cheapest credible fallback: public data, ~9,000 generations, under $30, one 24GB run. |
| Is it independent? | Yes: different question, data, models, analysis. E2's failure does not invalidate it. |
| Can we run both? | No: one paper, one budget, 3–4 people, same 24GB slot. |
| When switch? | E2 fails a gate, E2 is descoped, or its rental path closes. |

E2 is the primary bet. RANKCAL reuses the team's skills, not E2's artifacts, and can start quickly because the benchmark and models are public. If E2 succeeds, RANKCAL waits; if E2 fails a gate, RANKCAL becomes the paper. Running both at once would split attention and rental time.

---

## 13. Feasibility

**Roles.** Team of four; with three, writing is shared by the lead and the data/evaluation owner.

| Role | Responsibility |
|---|---|
| Lead / statistics | Design, DeLong, bootstrap, TOST, analysis, paper statistics |
| Inference | Harness, quantization, confidence extraction, rental, ledger |
| Data / evaluation | MMLU manifest, parsing, ARC/GSM8K, release artifacts |
| Writing | Paper, figures, reproducibility, supervisor updates |

**Compute and cash.**

| Item | Estimate |
|---|---|
| Primary generations | 2,500 × 2 = 5,000 |
| Secondary generations | 2,000 × 2 = 4,000 |
| Format-control arm | 500 (separate) |
| GPU time and cash | 4–8 hours on one 24GB card; under $30 |
| Local work | analysis and writing, $0 |

**Timeline.**

| Weeks | Work | Exit check |
|---|---|---|
| 1–2 | Pre-registration, manifest, harness, 200-item calibration | Parse failure < 5%; accuracy in [0.35, 0.85] |
| 3–6 | Primary run; Llama replication; ARC/GSM8K; format arm | Complete ledger; secondary labeled |
| 7–9 | Analysis: DeLong, bootstrap, TOST, isotonic, groups | Decision table applied |
| 10–18 | Paper, figures, reproducibility, buffer, release | Submission-ready |

**Dependencies.** One 24GB rental (RTX 3090/4090 class), Hugging Face access to Qwen2.5 and Llama-3.1 (gated), bitsandbytes, about 50GB of disk, no paid API. The local 6GB RTX 3060 is for smoke tests only; it cannot host a 7B model.

---

## 14. Risks and mitigations

| Risk | Why it matters | Mitigation |
|---|---|---|
| Accuracy outside [0.35, 0.85] | Floor or ceiling makes ΔAUROC uninformative | 200-item calibration gate; fix prompt/task; switch above 0.85 |
| MMLU saturation at 7B | Compressed range hides a rank change | Pre-registered switch to MMLU-Pro/ARC; MMLU secondary |
| Prompt or chat-template sensitivity | Format, not tuning, drives the result | Identical completion prompt; 500-item format arm; report a flip |
| Confidence extraction | Token variants or parsing errors distort AUROC | Max over surface variants; raw logits; unit tests |
| Subject clustering | Items within a subject correlate; naive SEs too small | Subject-stratified bootstrap and cluster-robust SE |
| Multiple comparisons | Secondary contrasts invite cherry-picking | One primary; gatekeeping; secondaries labeled |
| Group sample size | 510–790 items per group; MDE about 0.056 | Descriptive only; point-estimate guardrail |
| Model-family generalization | Two families, 7–8B, open weights only | Bounded claim; no frontier or API claim |
| Compute or rental | 24GB required; local 6GB cannot run 7B | Book early; wait rather than substitute |
| Reviewer expectations | "Where is the mechanism?" or "N is too small" | Measurement/equivalence framing; both CIs; no mechanism claim |

---

## 15. Deliverables

1. **Paper.** A tax paper or an equivalence paper, with a bounded claim, the pilot disclosed, and both 95% and 90% intervals.
2. **Code.** Item-manifest builder, inference harness, confidence extractor, DeLong/bootstrap/TOST analysis, isotonic calibration, figure scripts.
3. **Item manifest.** 2,500 item IDs with subject, group, split, seed, and hashes, frozen before model outputs.
4. **Raw outputs and reproducibility.** Generations, parsed answers, raw option logits, confidences, run ledger, environment lock, model revisions, and prompts.
5. **No new dataset.** No benchmark text redistribution, no human labels, no model weights.

---

## 16. RANKCAL in 60 seconds

> Instruction tuning usually makes a model more accurate. We ask whether it also makes the model worse at ranking its answers by confidence. Accuracy is how often it is right. Calibration is whether its confidence numbers match its hit rate. Ranking is whether correct answers get higher confidence than wrong ones. The pilot raised accuracy by 3.2 points but lowered AUROC by 3.9 points, with a confidence interval that still covers zero. So we are scaling up: 2,500 MMLU items, 7B base and Instruct models, a second model family, and a pre-registered equivalence test. A confirmed tax and a true null are both publishable. The run costs under $30 and 4–8 hours of GPU time.

---

## 17. FAQ for team members

**Why not just use ECE?** ECE measures the size of confidence; AUROC measures its order. Isotonic can lower ECE without reordering, so calibration can hide a ranking problem.

**Why not 1,500?** At 1,500, item MDE is 0.0355 and cluster MDE is 0.0427; TOST ±.03 power is 0.53 item / 0.26 cluster. It cannot resolve 0.03.

**Why 2,500?** It is the smallest N where item MDE (0.028) is below the margin and item-level TOST ±.03 power is 0.82; cluster ±.04 power is 0.85.

**What if the result is null?** A powered equivalence result is publishable: "No meaningful rank tax at 7B." It needs the 90% CI inside ±.03, not just p > .05.

**Why base versus Instruct?** They share pretraining, tokenizer, and architecture, so the contrast is the vendor's post-training transition. It is not a single-step ablation.

**Why no chat template in the primary?** Base models have none. Using one only for Instruct would confound format with tuning; the 500-item format arm measures that.

**Why is κ not a gate?** Its implied bound (0.192) is more than six times the 0.03 effect, so it cannot test the hypothesis.

**Why not run both E2 and RANKCAL?** They need the same 24GB slot, team, and separate papers. One paper, one budget; RANKCAL is the fallback.

**What is the cheapest first step?** CPU-only: freeze the manifest, write the analysis code, test it on synthetic logits. The first GPU step is the 200-item calibration.

---

## 18. Glossary

| Term | Meaning |
|---|---|
| Instruction tuning | Training on instruction–response pairs so a model follows requests |
| Base model | Model before instruction tuning; predicts next token, not trained to follow requests |
| Instruct model | Released sibling after instruction tuning and alignment |
| Logit | Raw token score before softmax |
| Softmax | Turns logits into probabilities summing to one |
| Confidence | Probability assigned to the chosen answer |
| AUROC | P(correct item outranks wrong item) |
| AURC | Area under the risk–coverage curve; lower is better |
| ECE | Weighted confidence–accuracy gap |
| Isotonic regression | Monotone recalibration; cannot reorder items |
| DeLong test | Paired test for two correlated AUROCs |
| Bootstrap | Resampling for uncertainty |
| Clustered SE | SE allowing within-subject correlation |
| MDE | Smallest detectable effect at 80% power |
| TOST | Two one-sided equivalence tests |
| Equivalence | Effect smaller than the meaningful margin, not exactly zero |
| Selective prediction | Answer when confident; abstain otherwise |
| Ranking tax | Ranking loss from instruction tuning |

---

## 19. References

- Hendrycks et al. (2021). Measuring Massive Multitask Language Understanding. *ICLR 2021*. arXiv:2009.03300.
- DeLong, DeLong, & Clarke-Pearson (1988). Comparing the areas under two or more correlated ROC curves. *Biometrics* 44(3), 837–845.
- Schuirmann (1987). A comparison of the two one-sided tests procedure and the power approach for assessing equivalence. *J. Pharmacokinet. Biopharm.* 15(6), 657–680.
- Guo et al. (2017). On Calibration of Modern Neural Networks. *ICML 2017*. arXiv:1706.04599.
- Geifman & El-Yaniv (2017). Selective Classification for Deep Neural Networks. *NeurIPS 2017*. arXiv:1705.08500.
- Wei et al. (2022). Finetuned Language Models Are Zero-Shot Learners. *ICLR 2022*. arXiv:2109.01652.

---

## Appendix A — Pilot numbers and recomputation

| Quantity | Pilot value | Independent recheck |
|---|---:|---:|
| Accuracy, base → Instruct | 0.512 → 0.544 | 0.512 → 0.544 |
| AUROC, base → Instruct | 0.7652 → 0.7259 | 0.7652088 → 0.7258611 |
| ΔAUROC | -0.0393 | -0.0393477 |
| DeLong SE | — | 0.0217990 |
| DeLong 95% CI | [-0.0821, +0.0034] | [-0.0820730, +0.0033776] |
| Item MDE at N=500 | ≈ 0.061 | 0.0610718 |
| κ_all / κ_Y | 0.593 / 0.615 | 0.5931062 / 0.6154456 |
| ECE, base → Instruct | 0.1333 → 0.1818 | Δ = +0.0485 |
| Isotonic ECE | 0.069 → 0.062 | closes 84% of the gap |

Pilot details: 500 rows, 57 subjects, Qwen2.5-1.5B base and Instruct, 4-bit, T=0, fixed completion prompt, no chat template. Item bootstrap SE at N=500 is 0.02170; the delete-one-subject jackknife SE is 0.02342; the between-subject SD is 0.00857. Group point estimates at N=500: STEM -0.0466, Humanities -0.0701, Social sciences -0.0422, Other -0.0190. All are negative; no group CI excludes zero.

The pilot is a direction, not a confirmation: the CI covers zero, the MDE exceeds the observed effect, and the 57-subject leg was untestable.

---

## Appendix B — Exact next-run specification and stop conditions

1. Freeze the 2,500-item subject-stratified MMLU manifest (seed 0, four groups, SHA-256 per item). No model output before the freeze.
2. Run the 200-item calibration on Qwen2.5-7B base and Instruct: parse failure < 5%, both accuracies in [0.35, 0.85], confidence checks pass. If it fails, fix and rerun; do not start the main run.
3. Run 2,500 × 2 primary models (4-bit, T=0, completion prompt, no chat template), storing generation, parsed letter, raw option logits, and confidence.
4. Run the 500-item chat-template arm on Instruct; run Llama-3.1-8B base and Instruct on the same 2,500; run ARC 1,000 and GSM8K 1,000 on the primary pair. Label Llama and the two benchmarks replication.
5. Analyze: paired DeLong; subject-stratified bootstrap 10,000; ΔAURC/ΔECE bootstrap; 5-fold isotonic; TOST ±.03/±.04; group descriptives; κ diagnostic.
6. Apply the decision table; report both 95% and 90% intervals.

**Stop conditions.** Parse failure > 5% or accuracy outside [.35, .85] after one calibration fix: stop; do not interpret. Equivalence: write the null paper. Inconclusive: stop. Confirmed tax: write. Small real tax: measurement result. Guardrail: withdraw "systematic." κ is diagnostic only.

---

## Appendix C — MMLU group mapping

| Group | n | Subjects |
|---|---:|---|
| STEM | 19 | abstract_algebra, anatomy, astronomy, college_biology, college_chemistry, college_computer_science, college_mathematics, college_physics, computer_security, conceptual_physics, electrical_engineering, elementary_mathematics, high_school_biology, high_school_chemistry, high_school_computer_science, high_school_mathematics, high_school_physics, high_school_statistics, machine_learning |
| Humanities | 13 | formal_logic, high_school_european_history, high_school_us_history, high_school_world_history, international_law, jurisprudence, logical_fallacies, moral_disputes, moral_scenarios, philosophy, prehistory, professional_law, world_religions |
| Social sciences | 12 | econometrics, high_school_geography, high_school_government_and_politics, high_school_macroeconomics, high_school_microeconomics, high_school_psychology, human_sexuality, professional_psychology, public_relations, security_studies, sociology, us_foreign_policy |
| Other / professional | 13 | business_ethics, clinical_knowledge, college_medicine, global_facts, human_aging, management, marketing, medical_genetics, miscellaneous, nutrition, professional_accounting, professional_medicine, virology |

Pre-registered verbatim. Groups are descriptive; the pooled ΔAUROC is the only confirmatory test. Counts are approximate until the manifest freezes.
