# Research Selection Package — One-File Review Copy

**Date:** 5 November 2026  
**Team:** 3–4 person CS team  
**Timeline:** 6–7 months  
**Compute:** local RTX 3060 6 GB, free Colab/CPU, public data, small open models  
**Cash ceiling:** **$50 total**, no paid API  
**Target:** Q1 journal; an 8+ core only if it genuinely exists

**How to use this file:** this is the self-contained review copy. It includes the verdict, the corrected scoring, the full parameter tables, the dataset and experimental details for the top three candidates, the rejected 8+ claims, and the claim-level audits that must pass before any 6-month commitment.

> **Latest update (8 November 2026):** all six Round-10 gates are complete. The current main-track leader is **E2 (Core 7.38 / Weighted 7.55)**, a staged GO with 800 real τ-bench prefixes, 3 open-weight 7–8B models, and a $70 hard cap. **T1’s mean-radius theorem was killed** and T1 falls to 5.63/6.18. For the final ranking, gate outcomes, and one-page-per-idea pack, use [`round10/index.md`](round10/index.md) and [`round10/ideas/`](round10/ideas/); for independently checked pilot evidence, use [`round9/FINAL_SURVIVORS_REVIEW.md`](round9/FINAL_SURVIVORS_REVIEW.md). Earlier score/ranking sections below are historical unless explicitly superseded there.

---

## 1. Executive verdict

**No verified 8+ idea exists in the searched space. Fifteen high-ceiling claims were desk-checked; every one failed.** The last three from the original search, **Trace Identifiability**, **M2**, and **I1**, were killed by independent 2–3 day audits. A further **five fresh directions and three 8+ contribution types** were audited in Round 4 (5 November 2026); **all failed**. The best fresh executable is **I4 + StyleCal-10k at 6.55 weighted / 6.17 core** — a real 6.5 paper, not an 8+ paper.

| Claim | Revised core | Fatal issue |
|---|---:|---|
| Restricted-alignment abstraction | 4.30 | False as stated; repair assumes away the question |
| Minimax experiment design under silent failures | 4.86 | Reduces to cost-aware best-arm identification with biased feedback |
| Mergescope (merge-level tokenizer Shapley) | 5.81 | Theorem 2 false; Theorem 1 trivial; estimand convention-dependent |
| ReasonBound (token-level CoT mediation) | 5.11 | Known LP (Miles et al. 2017); estimand conflated; exclusion restriction not credible |
| P2 unlearning audit | 5.17 | Exponent wrong by \(n\) or \(n^2\); queries are not samples |
| M1 network sensitivity | 5.60 | Marginal NMSM contains the point-treatment MSM; no strict tightening |
| Witness coverage | 5.72 | Collinear with MorphScore (Spearman .88); no strict matched pairs |
| **Trace Identifiability** | **4.08–4.75** | **CC bound false for a centralized trace generator; estimand undefined; published counterexamples** |
| **M2 joint confounding + misclassification** | **5.17** | **Strictness theorem false by nesting; joint set contains the marginal union; LP only in a special case** |
| **I1 protocol-relative credit** | **5.75** | **Strategic theorem false under full joint support; closest identification paper (Zhang 2026) pre-empts; repaired result is standard OPE + Shapley** |

**No 7+ candidate remains from the original search.** The best remaining *unaudited* option is **duplication influence (6.86 / 7.0)**, followed by **CBM (6.7 / 7.25, on hold)**. Based on the base rate of this search, expect duplication to lose 1–2 points under a 2–3 day audit.

### Round 4 — five fresh directions and three 8+ contribution types

Five non-hallucination, non-FYDP, non-IntentGate directions were audited, plus three contribution types: **T1** identification, **T2** lower bound, **T3** controlled dataset. **No idea + contribution type reaches 8+.** The full matrix and reports are in `archive/round4/09_synthesis.md`.

| Idea | Idea audit (core / weighted) | Best contribution | Best verified | 8+? |
|---|---:|---|---:|---|
| I1 script effect | 5.42 / 5.98 | T3 dataset | **6.10** | No |
| I2 code-switched CoT | 5.00 / 5.70 | — | **5.70** | No |
| I3 multilingual safety | 3.80 / 4.90 | — | **4.90** | No |
| I4 calibration tax | 3.67 / 4.60 | T3 StyleCal-10k | **6.55** | No |
| I5 ICL influence | 4.50 / 5.08 | T3 benchmark | **5.68** | No |

**What killed them:** I1 by *Script as a Safety Signal* (OpenReview `AL6ekXdgiP`), *Script Gap* (2512.10780), and *Script Tax* (2602.11174); I2 by *CoRe* (2604.15490, 80,532 responses); I3 by *CSRT* (2406.15481), *Minionese* (2607.10112), *TukaBench* (2606.01322), and *BanglaShield* (2610.24288); I4 by *Hu et al.* (2510.17426), which already names and measures the calibration cost; I5 by *Zhou et al.* (IEEE TKDE 2025), *Nguyen & Wong* (2302.11042), and *AttriBoT* (2411.15102).

**Contribution types:** T1's best is I5 at 6.95, but the theorem is a Lipschitz triangle inequality and the estimand is pre-empted; T2's best is I1 at 6.50, but it is a measurement paper in disguise; T3's best is I4 at 6.55. **None reaches 8+.**

**Verified local model:** `qwen3.5:4b` via Ollama — 4.2B, Q4_K_M, **3.3 GB**, 262k context, `think=false`, **30–77 tok/s**, clean JSON, Bengali script. It makes the T3 datasets feasible without paid API.

### Round 5 — style-calibration theory proof check

The 8+ “style as a calibration knob” theory was killed by three independent checks: model/lemma **4.45**, Pareto/optimal tilt **4.45**, prior art **5.5**. The **prompt-based** pilot scored **7.0** and supports the 6.5 dataset paper: hedged style cut confidence by **19.3 pp** with **0.0 pp** accuracy change on 98 MMLU items; ECE **0.188 → 0.120** (CI includes 0 at \(n=98\)). The theory is dead; the dataset paper is **conditional on the real fine-tuning pilot (Round 7)**. Full synthesis: `archive/round5/05_synthesis.md`.

### Round 6 — three decision-first pre-flights

All three were killed: **A** evidence budget **4.45** (Miller 2024, Eq. 9 — standard paired sample-size formula); **B** adaptive contamination **4.00** (special case of adaptive data analysis; arXiv:2609.32109); **C** calibration transfer **4.60** (importance-weighted VC; arXiv:2609.38917). No 8+ path. Full synthesis: `archive/round6/04_synthesis.md`.

### Round 7 — StyleCal pilot and PT→IT frontier

The real fine-tuning pilot **failed at Pythia-410M** (6.15): the model never learned MMLU (accuracy ~0.24–0.265, chance 0.25), and the large calibration shifts were **letter collapse**, not style. Matched-prefix ΔECE was +0.0089 (hedged) and −0.0317 (overconfident); accuracy moved +2.3/+2.5 pp. The PT→IT frontier theory was killed (4.75) and the experiment was negative (5.9): λ = 0.5 improves accuracy but worsens ECE; zero strict dominators. The **Qwen2.5-0.5B-Instruct pilot also failed**: base accuracy 42.6%, but removing the wrapper collapsed the style models (hedged 26.3%, overconfident 28.7%); the in-distribution ECE change was −0.045 (wrong sign), and the two style arms were indistinguishable. The effect is **wrapper-bound readout**, not style-driven calibration. **StyleCal-10k is dead.** Full synthesis: `archive/round7/04_synthesis.md`; final verdict: `archive/round7/06_final_verdict.md`.

### Round 9 — current surviving-only review (8 November 2026)

The previous ranking is superseded by the independent raw-ledger review in `round9/FINAL_SURVIVORS_REVIEW.md`. Its table includes only non-killed ideas, uses one consistent Round-8 score rubric, recalculates weighted/core arithmetic, and corrects E1 for active-constraint-load confounding. Current top three by evidence-adjusted Core: **T1 watermark 7.63**, **E2 fixed-prefix sensitivity 7.38**, **R1 scoped retrieval lift 7.38**. None is yet a verified 8+ paper. Full per-pilot checks, corrected score vectors, and next-stage conditions are in the Round-9 review.

### Final ranking (by weighted score; status is the decision filter)

| Rank | Candidate | Core | Weighted | Status |
|---:|---|---:|---:|---|
| 1 | **CBM** — certificate monitorability | 6.7 | **7.25** | On hold; bound not established |
| 2 | **Duplication influence** | 6.86 | **7.0** | Unaudited backup |
| 3 | **I4 + StyleCal-10k** | 6.17 | **6.55** | **KILLED by pilot (Pythia 6.15; Qwen failed)** |
| 4 | I1 + token-matched dataset | 5.33 | 6.10 | Dataset paper |
| 5 | I2 code-switched CoT | 5.00 | 5.70 | Killed |
| 6 | I5 ICL influence | 4.50 | 5.08 | Killed |
| 7 | I3 multilingual safety | 3.80 | 4.90 | Killed |
| — | I1, M2, Trace methods notes | 5.5–5.75 | 6.0–6.6 | Methods/measurement only |

**Recommended next move:** the 8+ path is closed and **StyleCal-10k is dead**. The best remaining unaudited option is **duplication influence (6.86 / 7.0)**; audit it before committing. **CBM (7.25)** remains on hold because its bound is not established. If neither passes, take a 5.5–6.0 measurement note or stop. Do **not** run another broad scan.

### Why Trace was killed

Two independent 2–3 day audits:

**Theorem audit (core 4.75 / weighted 5.50).** Standard one-way communication complexity is a two-party quantity. The trace generator sees the whole input, so the model is not a protocol:
- Under the centralized interpretation (Alice holds \(x\), Bob nothing), the one-way CC is at most the answer length — one bit for Boolean \(f\). The bound is vacuous.
- Under a split input, the model is no longer solving the original task.
- Counterexample: equality with \(T=f(x)\) has \(\ell=1\), \(\mathrm{CC}=n\), and \(\epsilon=0\); the claimed bound fails.
- A \((1,1)\)-BAPO solves Equality, Disjointness, and Index with constant bandwidth (Schnabel et al. 2025), so CC is not a lower bound on single-input traces.
- The empirical prediction is contradicted on three of six tasks: parity and majority need \(\Theta(n)\) CoT steps (Amiri et al. 2025; Tomlinson et al. 2026), while equality/disjointness are BAPO-easy.
- Closest: Mohammadkhani et al. 2026 (arXiv:2609.37312), *Hidden Reasoning Must Leak, but Need Not Be Readable*; Tomlinson et al. 2026 (arXiv:2602.02909); Amiri et al. 2025 (arXiv:2502.02393); Schnabel et al. 2025 (arXiv:2505.08140).

**Estimand/empirical audit (core 4.08 / weighted 4.95).** The gap needs a pair distribution, trace equivalence, a computation label, and a monitor class; none is defined. Exact trace equality is too strict, semantic equivalence is undecidable, and the model's computation is latent. Training on input→trace→answer makes the trace faithful by construction, so the experiment measures imitation of reference traces. The checksum leaks the answer; the construction that works is a keyed random pointer, a cryptographic existence proof. Closest: Anwar et al. 2026 (arXiv:2602.18297) on information-theoretic monitorability; Meek et al. 2025 (arXiv:2510.27378) on faithfulness + verbosity.

**Survivor:** a 5.5–6.0 measurement note using certificate complexity and monitor-capacity calibration, not a 7.17 theory paper.

### Why M2 was killed

The 2–3 day prior-art and sharpness audit found three fatal issues:

1. **The strictness theorem is false by nesting.** The single-bias models are special cases of the joint model (\(\delta=0\), \(\Gamma=1\)), so
   \[
   \Theta(\Gamma,0)\cup\Theta(1,\delta)\subseteq\Theta(\Gamma,\delta).
   \]
   The joint set can never be strictly narrower than the intersection of the marginal sets; it is a superset of their union. In the audit DGP (\(\tau=0.20\), naive \(=0.174\), \(\Gamma=4\), \(\delta=0.25\)): conf-only \([-0.193,0.556]\), mis-only \([0.174,0.348]\), intersection \([0.174,0.348]\), joint \([-0.261,0.589]\). A 60-DGP sweep found **60/60 joint sets strictly containing the intersection, 0/60 inside**.
2. **The "LP" is exact only in a special case.** For fixed treatment and misclassification probabilities, the per-cell feasible set is a polytope. The full sharp set over bounded \((\Gamma,\delta)\) is a union of polytopes / semialgebraic program, not one LP.
3. **The compensation frontier is not new.** It is a 2-D version of the multi-bias E-value. **Smith, Mathur & VanderWeele (2021, Epidemiology 32(5):625–634)** already give a conservative composite bound for unmeasured confounding + differential exposure misclassification and a multi-bias E-value. Díaz–van der Laan (2013) does **not** pre-empt the joint result, but it does pre-empt the 1-D bias-bound framing.

**Correct theorem:** the one-line nesting result above. The hard part is continuous-\(X\) estimation/inference (nonsmooth bound functional, unidentified propensity, curse of dimensionality), a 6–12 month theory project by itself.

**Score:** 5.17 core / 5.78 weighted. **Survivor:** at most a 5.75 / 6.30 methods note, *“Marginal bounds are not joint bounds: a semialgebraic computation for MSM confounding and misclassification,”* in *J. Causal Inference* or *Biometrics*. Not a 7.3 theory paper.

### Why I1 was killed

The 2–3 day audit of the last 7+ backup found:

1. **The closest identification paper was missed.** Zhang 2026, *“Value Identifiable, Credit Not”* (AAMAS 2026 CLaRAMAS, OpenReview `1LbCpjOpAv`) already formalizes credit with a continuation policy and proves value-identifiability \(\neq\) credit-identifiability via a row-space criterion and LP bounds. 2605.27621 has protocol-conditioned attribution but no identification theory; the scan missed Zhang.
2. **The point-identification theorem survives only after repair.** The scan’s action-positivity condition ignores state support and joint support; \(\sqrt T\)-consistency needs bounded or finite-variance importance weights. The repaired result is standard off-policy evaluation plus a linear Shapley map — a special case of Zhang’s criterion.
3. **The strategic theorem is false as stated under full joint support.** Full joint support identifies rewards and transitions, so there are no free off-path payoffs. The true result is that **missing joint actions** break identification; strategic protocols amplify the break.
4. **The reversal is support-driven, not strategic.** A 3-agent/2-action example with two pure equilibria and identical on-path data gives completion A ranking 1>3>2 and completion B ranking 2>3>1. Reversal rate: 92.7% at \(k=7\) (one missing joint action), width 0.14; ~100% at \(k=2\)–6, width 0.22–0.33; 0% at full support.
5. **The LP is sharp only in the known-transition/reward-only case.** Unknown transitions make the joint Shapley set a semialgebraic projection; the strategic case is a union of polytopes.

**Score:** 5.75 core / 6.60 weighted. **Survivor:** a safe 6.0 methods paper, not a 7+ theory paper.

---

## 2. Scoring methodology and correction

### 2.1 Parameters and weights

| # | Parameter | Weight | What a high score means |
|---:|---|---:|---|
| 1 | **Novelty** | 20% | The closest papers do not already contain the same estimand, assumptions, method, and guarantee |
| 2 | **Method** | 15% | A new reusable estimator/algorithm/design, not a new application |
| 3 | **Math** | 15% | A substantive identification result, theorem, bound, or minimax result |
| 4 | **Experiments** | 10% | A cheap experiment can show the effect is real and large, with ground truth |
| 5 | **Dataset** | 5% | A reusable artifact that enables a scientific claim |
| 6 | **Q1 fit** | 10% | A credible Q1 venue where the contribution is important |
| 7 | **Feasibility** | 10% | 3–4 people can finish in 6–7 months |
| 8 | **Compute fit** | 5% | The core experiments fit the 6 GB 3060 / free Colab |
| 9 | **API/cash** | 5% | Low spend; no frontier-model dependency |
| 10 | **Independence** | 5% | Not a superficial extension of FYDP/HaluRISC/IntentGate |

**Core** = mean of parameters 1–6.  
**Weighted** = sum of parameter score \(\times\) weight.

### 2.2 Correction after external review

Earlier drafts reported inconsistent weighted scores. Recalculated with the stated weights:

| Candidate | Earlier reported | Corrected | Arithmetic |
|---|---:|---:|---|
| Trace Identifiability | 7.35 | **7.55** | \(0.20(7)+0.15(7)+0.15(7)+0.10(8)+0.05(7)+0.10(7)+0.10(8.5)+0.05(9)+0.05(10)+0.05(8)\) |
| CBM | 7.80 | **7.25** | \(0.20(7)+0.15(6.5)+0.15(7)+0.10(7)+0.05(5.5)+0.10(7)+0.10(8)+0.05(9)+0.05(10)+0.05(8)\) |
| M2 | 7.60 | **7.56** | \(0.20(7.5)+0.15(7.4)+0.15(7.5)+0.10(7)+0.05(6.5)+0.10(7.5)+0.10(7)+0.05(8)+0.05(10)+0.05(9)\) |

**Consequence:** CBM is **not** the highest-weighted candidate. The corrected order is M2 7.56, Trace 7.55, CBM 7.25. The weighted scores are close enough that they should not drive the decision; team fit and the audits matter more.

### 2.3 Status labels

- **Desk-checked / killed:** a 2–3 day adversarial check found the central theorem or design false.
- **Scan score / audit required:** the score comes from an idea scan; the central theorem has not been independently checked.
- **On hold:** a known theoretical gap blocks commitment.

---

## 3. Why the search stopped at 7.3

The pattern across seven failures is consistent:

1. **Scan scores were optimistic by 1.5–2.8 points.** Every 8.0 that was checked lost at least 2 points.
2. **The closest-paper check was decisive.** Mergescope missed Lesci et al. (ACL 2025) on causal subword inclusion; ReasonBound missed Miles et al. (2017) on partial identification of the NIE; P2 missed the paired-test variance and influence linearization; M1 missed the point-treatment MSM containment.
3. **A new application is not a new theorem.** Token-level Shapley, token-level NIE, audit power, and network sensitivity were new applications of known mathematics.
4. **The $50 budget was almost never the binding constraint.** The binding constraints were theorem novelty, proof difficulty, and experiment power.
5. **Small-model experiments are cheap but underpowered.** Mergescope's 3-seed ICC gate passes only ~48% of the time at the true threshold; ReasonBound's 500-prompt bounds are likely vacuous.
6. **Model-level containment is a common failure.** If the new model contains the old one as a special case, the new “sharp bound” cannot be a strict tightening. This killed M1 and contributed to P2.
7. **Collinearity is the measurement-paper killer.** If the new metric is 0.88 Spearman with the old metric, it is not a new construct. This killed witness coverage.

**Implication:** more broad scans are unlikely to help. The next stage must be claim-level verification, not idea generation.

---

# Part I — Candidate A: Trace Identifiability

> **STATUS: KILLED (5 November 2026).** Two independent 2–3 day audits revised the core to **4.08–4.75** and the weighted to **4.95–5.50**. The 7.17 / 7.55 scores in this section are the original scan scores, retained only for the audit trail. **Do not start this project.** The only survivor is a 5.5–6.0 measurement note using certificate complexity and monitor-capacity calibration.

## 4. One-page brief

**Question.** When does a chain-of-thought trace actually *certify* the computation that produced the answer, rather than merely being correlated with it?

**Problem.** A model can produce a trace that is causally necessary for its answer — intervening on the trace changes the answer — yet the trace still does not identify *how* the computation was done. The trace may be a checksum, a post-hoc narrative, or a shortcut. Existing faithfulness metrics measure causal influence, not identifiability.

**Estimand.** The **trace identifiability gap**: the maximum fraction of input pairs with different answers that share the same trace; equivalently, the minimum trace length needed for a trace-only monitor to distinguish computations.

**Proposed theorem.** Treat the trace as a one-way message from the computation to an interpreter. If the task family has one-way communication complexity \(\mathrm{CC}(f)\), then any \(\epsilon\)-identifying trace of length \(\ell\) must satisfy

\[
\ell \ge \frac{(1-\epsilon)\,\mathrm{CC}(f)}{\log |V|}.
\]

A shorter trace cannot distinguish some pair of inputs with different answers. The required trace length would then be a **task property**, not a model property.

**Testable prediction.** Parity has \(\mathrm{CC}=O(1)\), so a short trace can certify it. Graph connectivity has \(\mathrm{CC}=\Omega(n)\), so a fixed 10-token trace is necessarily unfaithful on large graphs.

## 5. Full parameter scores (out of 10)

| # | Parameter | Score | Comment |
|---:|---|---:|---|
| 1 | **Novelty** | **7.0** | New estimand; the underlying communication bound is known |
| 2 | **Method** | **7.0** | Trace-corruption design + CC-matched task suite; no new estimator |
| 3 | **Math** | **7.0** | Clean one-way communication lower bound; standard, not a new inequality |
| 4 | **Experiments** | **8.0** | Six tasks with known CC; cheap, controlled, decisive |
| 5 | **Dataset** | **7.0** | Synthetic tasks + public reasoning benchmarks; no new natural dataset |
| 6 | **Q1 fit** | **7.0** | TACL / Computational Linguistics / JMLR |
| 7 | **Feasibility** | **8.5** | 3–4 people, 6–7 months, mostly local |
| 8 | **Compute fit** | **9.0** | 1–10M-parameter transformers fit 6 GB easily |
| 9 | **API/cash** | **10.0** | $0–10; no paid API |
| 10 | **Independence** | **8.0** | Distinct from FYDP/HaluRISC/IntentGate |
| | **Core** | **7.17** | Original scan score; **killed** |
| | **Weighted** | **7.55** | Original scan score; **killed** |

*Audit-corrected scores: **4.08–4.75 core / 4.95–5.50 weighted**. The table above is the original scan score, retained for comparison. Do not use 7.17 for selection.*

## 6. Theorem audit required before commitment

The central theorem is **not yet justified** by the current task definitions. The communication model is underspecified. The audit must answer:

1. **Input partition.** Who knows which parts of the input? Standard communication complexity is two-party; the reasoning task is single-input. Define the partition explicitly (e.g., Alice holds the first half, Bob the second; or Alice holds the graph, Bob the query).
2. **Trace-generation protocol.** What computation produces the trace? Is it a deterministic algorithm, a learned model, or an arbitrary protocol? The bound applies to a protocol, not to a post-hoc text.
3. **Monitor access.** Does the monitor see only the trace, the trace plus the input, or can it query the model? If it sees the input, the relevant quantity is **conditional** communication complexity, which can be much smaller.
4. **Computational bounds.** Is the monitor computationally unbounded? If bounded, the lower bound may fail for reasons unrelated to the trace.
5. **Communication model.** Deterministic, randomized, distributional, or worst-case? Equality and majority have different randomized vs. deterministic complexity; the theorem must fix one model.
6. **Trace vocabulary and length.** How does the token vocabulary \(V\) map to the communication channel? Is \(\ell\log|V|\) the right bit count? Are tokens fixed-length?
7. **Input-pair distribution.** The identifiability gap is distribution-dependent. Specify the sampling distribution over input pairs; otherwise the estimand changes with the dataset.

**Decision rule:** if the communication model cannot be made correct and non-trivial within 2–3 days, stop or reformulate. Do not spend six months rescuing a weak theorem.

**Auxiliary literature check:** verify that the estimand adds something beyond existing CoT faithfulness (Paul et al. 2024; Fu et al. 2026; Wei et al. 2026) and information-theoretic measures of trace sufficiency.

## 7. Dataset and experimental plan

### 7.1 Task suite

The core dataset is **synthetic and built by you**, not downloaded. That is what makes the CC labels exact. Public benchmarks are only for external validation.

| Task | Input | Answer | Reference trace | One-way CC (deterministic) | Role |
|---|---|---|---|---|---|
| **Parity** | \(n\)-bit string, \(n=8\)–32 | XOR of bits | Running XOR token sequence | \(\Theta(1)\) bit | Low-CC anchor |
| **Majority** | \(n\)-bit string, \(n=8\)–32 | 1 if ones \(> n/2\) | Running count / threshold sequence | \(\Theta(\log n)\) bits | Medium-CC |
| **Equality** | Two \(n\)-bit strings, \(n=8\)–32 | Equal / not equal | Left-to-right comparison sequence | \(\Theta(n)\) bits | High-CC; deterministic model |
| **Graph connectivity** | Random graph, \(n=8\)–16 vertices | Connected / not | BFS / union-find trace | \(\Omega(n)\) bits | High-CC, structured |
| **Set disjointness** | Two sets of size \(n=16\)–64 | Disjoint / not | Pairwise comparison trace | \(\Theta(n)\) bits (even randomized) | Hardest high-CC task |
| **Multi-digit addition** | Two \(n\)-digit numbers, \(n=3\)–6 | Sum | Digit-by-digit carry trace | \(\Theta(n)\) bits | Natural arithmetic; bridges to GSM8K |

**Design point:** the CC range must be wide enough that the bound is non-vacuous. With a small trace vocabulary (say \(|V|=16\), \(\log|V|=4\) bits/token), a 10-token trace carries 40 bits. Parity (1 bit) is easy; set disjointness with \(n=64\) (64 bits) is not. If all tasks have CC below the trace length already needed for accuracy, the bound is vacuous.

### 7.2 Data generation

- **Size:** ~100k training / 10k validation / 10k test examples per task (720k total). For graph connectivity, generate random graphs at several densities; for addition, sample digit lengths uniformly; for parity/majority, sample \(n\) uniformly from the chosen range.
- **Traces:** generated by a reference algorithm (running XOR, running count, left-to-right comparison, BFS/union-find, pairwise set comparison, carry propagation). The trace is a token sequence over a small task vocabulary, so trace length is measured in tokens.
- **Trace-length manipulation:** truncate the reference trace to 2, 4, 8, 16, 32 tokens; pad with neutral tokens; or let the model generate free-length traces. Truncation is the intervention that tests the CC bound.
- **Storage:** ~20–30 GB including model checkpoints and generated traces.

### 7.3 Models and training

- **Architecture:** decoder-only transformer, 2–6 layers, 128–256 dim, 4–8 heads, **1–10M parameters**; character-level or small task vocabulary.
- **Training:** from scratch on input → trace → answer; AdamW; 5 seeds per configuration; fixed token budget; early stopping on validation answer accuracy.
- **Why from scratch:** exact control over the training distribution and a meaningful CC bound. GPT-2/Pythia are used only for the external check, not the main claim.

### 7.4 Measurements and baselines

| Metric | Definition | What it tests |
|---|---|---|
| **Answer accuracy** | Standard held-out accuracy | Is the task learnable at this scale? |
| **Trace length** | Tokens in the generated trace | Does the model use the expected length? |
| **Identifiability gap** | Fraction of answer-different input pairs with identical traces; or \(1-\text{trace-only monitor AUROC}\) | Can a trace-only interpreter distinguish computations? |
| **Intervention effect** | Answer-change probability when a trace token is deleted/swapped, minus matched random-token control | Is the trace causally used, or post-hoc? |
| **Monitor AUROC** | Trace-only vs. input-visible vs. interactive interpreter | How much does interpreter access weaken the bound? |
| **Bound calibration** | Measured gap vs. predicted \((1-\ell\log|V|/\mathrm{CC}(f))\) | Does the CC law hold? |

**Baselines:**
- Answer-only + post-hoc trace (post-hoc rationalization).
- CoT-trained model (main).
- Length-matched random traces.
- Trace-only logistic interpreter.
- Input-visible interpreter.
- Interactive interpreter.
- **Checksum model:** computes the answer in a hidden state, writes a checksum trace, then reads the answer from the trace. The trace is causally necessary but contains no certificate — the key demonstration that faithfulness \(\neq\) identifiability.

**Statistics:** 5 seeds; paired seed comparisons; bootstrap over inputs for the identifiability gap and intervention effect; 95% CIs; pre-register the primary task set, primary metric, and trace-length grid; power calculation for the intervention effect.

### 7.5 Timeline and budget

| Phase | Work | Time |
|---|---|---|
| Months 1–2 | Define estimand, build six-task suite, reference traces, exact oracles | 6–8 weeks |
| Months 2–3 | Train CoT models, 5 seeds, trace-length grid | 4–6 weeks |
| Months 3–4 | Intervention faithfulness, identifiability gap, checksum model | 4–6 weeks |
| Months 4–5 | Interpreter-access ablation, external validation | 4 weeks |
| Months 5–6 | Theory cleanup, writing, TACL submission | 4–6 weeks |
| Month 7 | Buffer / rebuttal | 4 weeks |

**Budget:** 100–150 local GPU-hours + 50–80 Colab T4 hours; 20–30 GB; **$0–10 cash**. No paid API.

**External validation:** GSM8K (multi-step arithmetic), StrategyQA (multi-hop), Big-Bench Hard subset (boolean expressions, navigate, tracking shuffled objects); Pythia-70M/160M/410M/1B, Qwen2.5-0.5B/1.5B, Llama-3.2-1B; inference only.

### 7.6 Risks and kill criteria

1. **The trace is post-hoc.** If corrupting the trace does not change the answer more than a matched control, the model is not using the trace; the estimand is unmeasurable.
2. **The CC range is too narrow.** If all tasks have CC below the trace length already needed for accuracy, the bound is vacuous.
3. **The model solves the task without the trace.** Measure trace ablation.
4. **The communication model is ambiguous.** Fix it before writing the theorem.
5. **Synthetic-to-natural transfer.** Keep the external check as a robustness section.

**2–3 day kill check:** train a 1M-parameter transformer on parity and small graph connectivity with a fixed 8-token trace; corrupt one trace token at a time and measure the answer change.
- **Kill** if the effect is within 5 points of matched random-token corruption on both tasks.
- **Secondary kill** if the tasks’ CC values are all below the trace length already needed for accuracy.

### 7.7 Why 7.17, not 8+

- The theorem is a **standard one-way communication bound** applied to a new estimand. The novelty is the estimand and the empirical law, not the inequality.
- If the interpreter sees the input, the relevant quantity becomes **conditional** communication complexity, which can be much smaller and weakens the bound.
- The empirical CoT-faithfulness space is crowded (Paul et al. 2024; Fu et al. 2026; Wei et al. 2026). The paper must lead with the law and the measurement, not “another faithfulness metric.”

---

# Part II — Candidate B: M2 Joint Confounding + Misclassification

> **STATUS: KILLED (5 November 2026).** The 7.3 / 7.56 claim is false: the strictness theorem fails by nesting, the joint set contains the marginal union, and the LP is only a special case. Revised score: **5.17 core / 5.78 weighted**. Only a **5.75 / 6.30** methods note survives. The details below are retained for the audit trail.

## 8. One-page brief

**Question.** Treatment is measured by an imperfect classifier and assignment is also confounded by an unmeasured factor. What is the sharp identified set for the ATE as a function of confounding strength \(\Gamma\) and misclassification bounds \(\delta\)?

**Estimand.** The sharp joint set \([L(\Gamma,\delta),U(\Gamma,\delta)]\) and the **bias-compensation frontier** \(B(\Gamma,\delta)\): where confounding and misclassification have opposite signs, so the observed association can be closer to the truth than either marginal analysis suggests.

**Proposed theorem.** Under a surrogate assumption \(Y\perp T^*\mid(T,X,U)\), an MSM odds-ratio bound \(\Gamma\), and misclassification bounds \(\delta_T\), the sharp set is the projection of a polytope defined by the latent joint distribution; the bounds are sharp, monotone in \(\Gamma\) and \(\delta\), and the joint set can be strictly narrower than the intersection of the marginals. For every \(\Gamma>1,\delta>0\), there exist distributions with \(\tau=0\) but observed association arbitrarily far from zero, and vice versa.

## 9. Full parameter scores (out of 10)

| # | Parameter | Score | Comment |
|---:|---|---:|---|
| 1 | **Novelty** | **7.5** | Joint sharp bounds for two simultaneous biases; the joint region is not standard |
| 2 | **Method** | **7.4** | LP over the latent joint; text application |
| 3 | **Math** | **7.5** | Sharpness, monotonicity, compensation construction |
| 4 | **Experiments** | **7.0** | Synthetic binary data + semi-synthetic text application |
| 5 | **Dataset** | **6.5** | No new dataset; public tabular/text |
| 6 | **Q1 fit** | **7.5** | Biometrics / JRSS-B |
| 7 | **Feasibility** | **7.0** | Needs an LP sharpness proof; 6–7 months |
| 8 | **Compute fit** | **8.0** | 20–40 GPU-hours; small text models |
| 9 | **API/cash** | **10.0** | $0–30; no paid API |
| 10 | **Independence** | **9.0** | Distinct from FYDP/HaluRISC/IntentGate |
| | **Core** | **7.3** | Original scan score; **killed** |
| | **Weighted** | **7.56** | Original scan score; **killed** |

*Audit-corrected: **5.17 core / 5.78 weighted**. Repaired methods note: **5.75 / 6.30**. Do not use 7.3 for selection.*

## 10. Prior-art and sharpness audit required

M2 is potentially the strongest mathematics of the three, but novelty is the bottleneck. The audit must establish:

1. **Díaz–van der Laan (2013).** Does it already contain the joint sharp result for misclassified treatment? Read the full paper, not the abstract. If yes, M2 collapses to a text application.
2. **Joint vs. marginal.** Does the joint bound equal the intersection of the marginal bounds for all \(\Gamma,\delta\)? If yes, there is no new identification content. Construct a numerical counterexample to the claimed strict improvement.
3. **Compensation frontier.** Is \(B(\Gamma,\delta)\) a new mathematical result, or a visualization of known sensitivity behavior?
4. **Common assumptions.** The joint and marginal sets must share the same underlying assumptions; otherwise the comparison is not meaningful.
5. **Sharpness proof.** Is the LP sharp for continuous \(X\), or only for discrete \(X\)? State the minimal assumption set.

**Decision rule:** if Díaz–van der Laan already covers the joint result, or the joint bound equals the marginal intersection, kill M2 as a theory paper. Do not spend six months on a text application disguised as a theorem.

## 11. Dataset and experimental plan

- **Dataset:** synthetic binary \((U,T,T^*,Y)\) data with known confounding and misclassification; semi-synthetic text application on Yelp/IMDB sentiment (or MIMIC-III if accessible) using DistilBERT/MiniLM; public tabular data (Adult/COMPAS) for the LP.
- **Plan:** months 1–2 derive the LP, prove sharpness, simulate; months 2–3 joint vs. marginal bounds and the compensation region; months 3–4 text application; months 5–6 robustness and writing.
- **Budget:** 20–40 GPU-hours, ~150 CPU-hours, 10 GB, **$0–30**.
- **Kill gate:** the joint bound equals the intersection of the marginals for all \(\Gamma,\delta\), or Díaz–van der Laan (2013) already contains the joint result.
- **Main risks:** Díaz–van der Laan pre-emption; joint bound equals marginal intersection; weak text application; statistics-native, not CS-native.
- **Why 7.3, not 8+:** the joint region is not automatically more informative than the marginals; the novelty depends on the exact prior-art comparison; the text application is a secondary contribution.

---

# Part III — Candidate C: CBM Certificate Monitorability (on hold)

## 12. One-page brief

**Question.** When is a chain-of-thought intrinsically unmonitorable from traces, regardless of how faithful it is?

**Problem.** Causal faithfulness is not sufficient for monitorability. A trace can be causally necessary for the answer yet contain no certificate of the computation, so no trace-only monitor can tell a faithful computation from a post-hoc rationalization.

**Proposed estimand.** The **monitorability gap** \(\Delta_M = M - (\tfrac12 + 2^{-(C(A)-\kappa(T))})\), where \(C(A)\) is the certificate complexity of the monitored property \(A\), \(\kappa(T)\) is the certificate content of trace \(T\), and \(M\) is the best trace-only monitor AUROC. Secondary: the **faithfulness–monitorability divergence**.

**Proposed theorem sketch.** If \(\kappa(T)<C(A)\), at least \(2^{C(A)-\kappa(T)}\) computations with different \(A\)-values are consistent with the same trace distribution. Any monitor with false-positive rate \(\alpha\) has detection probability at most \(\alpha+2^{-(C(A)-\kappa(T))}\). If \(T\) contains a valid certificate, a sound and complete monitor exists. A checksum model separates high causal faithfulness from low monitorability.

## 13. Full parameter scores (out of 10)

| # | Parameter | Score | Comment |
|---:|---|---:|---|
| 1 | **Novelty** | **7.0** | New identification claim: faithfulness \(\neq\) monitorability |
| 2 | **Method** | **6.5** | Certificate-content probe + checksum separation; narrow |
| 3 | **Math** | **7.0** | Communication/covering argument; standard lower bound |
| 4 | **Experiments** | **7.0** | Synthetic certificate tasks; small models; decisive separation |
| 5 | **Dataset** | **5.5** | Synthetic only; weak dataset contribution |
| 6 | **Q1 fit** | **7.0** | TACL / AIJ |
| 7 | **Feasibility** | **8.0** | 350 GPU-hours; 6–7 months |
| 8 | **Compute fit** | **9.0** | 2–6-layer transformers fit 6 GB |
| 9 | **API/cash** | **10.0** | $0–10; no paid API |
| 10 | **Independence** | **8.0** | Distinct from FYDP/HaluRISC/IntentGate |
| | **Core** | **6.7** | Mean of parameters 1–6 |
| | **Weighted** | **7.25** | Corrected arithmetic |

## 14. Mathematical validity audit required

**Status: on hold.** The monitorability bound is not established. Certificate complexity alone does not imply distributional indistinguishability. The audit must establish:

1. **Distribution over computations and traces.** Define the probability space. A certificate-complexity number does not automatically give the claimed detection-probability bound.
2. **Indistinguishability assumption.** Prove that traces with different \(A\)-values are distributionally indistinguishable, or state the assumption explicitly.
3. **Monitor access.** Does the monitor see only the trace, or also the input? A monitor with input access can recompute the answer and ignore the trace.
4. **Computational bounds.** Is the monitor computationally unbounded? If bounded, the bound may fail for unrelated reasons.
5. **Overlap with Trace Identifiability.** CBM and Trace both ask what a trace can certify. Decide whether CBM is a corollary of Trace or a genuinely different theorem.
6. **Dataset weakness.** The synthetic certificate tasks give a dataset score of 5.5; a TACL/AIJ paper needs a stronger empirical contribution or a sharper theorem.

**Decision rule:** do not spend hundreds of GPU-hours before the bound is proved. If the bound cannot be established under explicit assumptions, merge the question into Trace Identifiability or drop CBM.

---

## 15. Comparison of the three directions

| Dimension | Trace Identifiability | M2 | CBM |
|---|---|---|---|
| **Core** | **4.08–4.75 (killed)** | **5.17 (killed)** | 6.7 |
| **Weighted** | **4.95–5.50** | **5.78** | 7.25 |
| **Primary data** | Six synthetic CC tasks + GSM8K/StrategyQA/BBH | Synthetic binary + Adult/COMPAS + Yelp/IMDB | Synthetic certificate tasks + public reasoning benchmark |
| **Dataset type** | Built (CC-annotated) | Public + semi-synthetic | Built (certificate-annotated) |
| **Compute** | 100–150 GPU-h + 50–80 Colab | 20–40 GPU-h + 150 CPU-h | 350 GPU-h |
| **Cash** | $0–10 | $0–30 | $0–10 |
| **Venue** | — (measurement note only) | J. Causal Inference / Biometrics (methods note) | TACL / AIJ |
| **Main risk** | Theorem false; estimand undefined | Strictness false by nesting; joint contains marginals | Bound not established; overlaps Trace |
| **Status** | **Killed by two audits** | **Killed by audit** | On hold |
| **Best for** | — | 5.75 / 6.30 methods note only | Merge into Trace or hold |

---

## 16. Rejected high-ceiling claims (detail)

### 16.1 Mergescope — 5.81 core / 6.08 weighted

Claim: Shapley value of each BPE merge on OOD compositional accuracy; identification on a pairwise-interaction merge forest; \(\Omega(K2^\tau)\) interventions.

Fatal issues:
- The estimand is not well-defined without a precedence convention. Closure Shapley and Faigle–Kern precedence Shapley differ; in a 3-merge example, the prerequisite's credit doubles.
- Theorem 1 is a two-line Shapley identity for any pairwise graph; the forest only reduces queries from \(O(K^2)\) to \(O(K)\).
- Theorem 2 is parameter counting, not a statistical lower bound, and is false for dense pairwise interactions. For \(K=4\), the claimed 32 queries vs. the true 10.
- Closest paper: Lesci et al. (ACL 2025), *Causal Estimation of Tokenisation Bias*, already estimates the causal effect of subword inclusion. Sälevä & Lignos (2023) find random BPE merges change downstream MT little.
- The 3-seed ICC gate is underpowered: at true ICC 0.3, \(P(\text{pass})\approx0.48\).

### 16.2 ReasonBound — 5.11 core / 5.43 weighted

Claim: token-level CoT mediation; sharp Fréchet–Hoeffding bounds under an intervention exclusion restriction.

Fatal issues:
- The estimand conflates a controlled step effect (point-identified by the splice), an interventional effect (identified by randomized replacement), and the cross-world natural/path-specific NIE.
- The LP is Miles et al. (2017), *On Partial Identification of the Natural Indirect Effect*.
- The exclusion restriction is a no-side-channel assumption; it does not address cross-world dependence or mediator–outcome confounding.
- The 30%-narrow-bound gate is unattainable under the stated assumptions.
- The full experiment is ~120–180 GPU-hours, not 80; 125+ hours of human validation are unbudgeted.

### 16.3 P2 unlearning audit — 5.17 core / 5.48 weighted

Claim: influence-bounded deletion signal; any audit with \(m\) shadows and \(q\) queries needs \(mq\gtrsim n^2/(\kappa^2\Delta^2)\).

Fatal issues:
- The two-sample reduction gives an additive constraint \(1/\sqrt m+1/\sqrt q\gtrsim\Delta\), hence \(m,q\gtrsim1/\Delta^2\), not \(mq\gtrsim1/\Delta^2\).
- At \(\Delta=\kappa/n\), the paired cost is \(O(1)\), the unpaired cost \(O(n)\), not \(10^{10}\).
- \(q\) black-box queries to one model are not \(q\) deletion samples.
- The stated auditability threshold is the reciprocal of the bound's implication.
- If the auditor knows \(D\) and \(z\), one retraining on \(D\setminus\{z\}\) yields \(\theta_r\) exactly, making the lower bound vacuous.
- Survivor: a 6.0–6.5 note, *“Queries Buy Direction, Not Samples.”*

### 16.4 M1 network sensitivity — 5.60 core / 6.33 weighted

Claim: sharp joint bounds for direct and spillover effects under unmeasured network confounding; network robustness value \(\Gamma^\ast\).

Fatal issues:
- The marginal NMSM contains the point-treatment MSM as a special case; the direct-effect sharp set contains the Dorn–Guo–Kallus bound, not a strict subset.
- The joint polygon is a generic zonotope from a shared control outcome, not a network identification theorem.
- The “exchangeable clusters” assumption is insufficient for sharpness; independent clusters are needed.
- Closest papers: Ortyashov & Ghassami (arXiv:2511.21534) on interference plus confounding; Schröder et al. (arXiv:2602.03459) on misspecified exposure mappings.
- Survivor: a 5.5–6.5 negative/measurement note.

### 16.5 Witness coverage — 5.72 core / 6.0 weighted

Claim: average morphological alignment is not sufficient for OOD composition; item-level witness coverage is.

Fatal issues:
- Coverage is collinear with MorphScore: Pearson .782, Spearman .880, partial .816 (.640/.838 on 14 standard tokenizers).
- Zero strict matched tokenizer pairs under exact vocabulary, \(|\Delta A|\le.05\), fertility 0.9–1.1, \(|\Delta O|>.2\). Best near-match \(\Delta O=.207\), only one contrast.
- The opacity formula counts occurrences, not distinct contexts.
- Closest: Coverage Principle (2505.20278) and MorphScore (2507.06378).
- Survivor: a ~6.0 test-weighted exact-match measurement paper.

### 16.6 Restricted-alignment abstraction — 4.30 core

Fatal issue: the restriction constrains the alignment \(\pi\), not the low-level model \(M\). A context-gated mechanism preserves atomic IIA and any atomic interaction norm while making composite IIA arbitrary. With a compositional-modularity assumption, the result collapses to naturality plus a triangle inequality (Lorenz & Tull 2026; Rischel & Weichwald 2021), i.e., it assumes the scientific question. The remaining pieces reduce to CRL/ICA with auxiliary variables, causal transportability, functional ANOVA, and standard experimental design.

### 16.7 Minimax experiment design under silent failures — 4.86 core

Fatal issue: the claimed lower bound reduces to cost-aware best-arm identification with biased feedback. Closest: Jedra, Al Marjani & Benomar, *Near Cost-Optimal Best-Arm Identification with LLM Judges* (EWRL 2026); CABAI (arXiv:2402.16710); NHSHT (arXiv:2509.11632); multi-fidelity BAI (arXiv:2406.03033); correlated BAI (arXiv:2109.04941); COLT 2026 biased-source collection (arXiv:2602.17894). “Standard BED is minimax-suboptimal” is either trivial (misspecified BED) or ill-posed (correctly specified BED is Bayes-optimal).

---

## 17. What was searched

| Scan | Class | Top idea | Score | Verdict |
|---|---|---|---:|---|
| K — low-budget | cross-domain | Mergescope | 8.0 → 5.81 | Killed |
| K — low-budget | cross-domain | ReasonBound | 7.9 → 5.11 | Killed |
| L — learning theory | small transformers/NLP | Merge-boundary interference | 8.0 → 6.5–7.0 | Downgraded |
| L — learning theory | small transformers/NLP | Trace identifiability | 7.17 → 4.08–4.75 | **Killed by two audits** |
| N — NLP semantics | tokenization/linguistics | Witness coverage | 7.6 → 5.72 | Killed |
| O — LLM reasoning | CoT/planning/ICL | CBM | 6.7 / 7.25 | On hold |
| P — optimization | generalization/memorization | Duplication influence | 6.86 / 7.0 | Backup |
| Q — multi-agent | MARL/game theory | Protocol-relative credit | 7.07 → 5.75 / 6.60 | **Killed by audit** |
| R — human–AI | deferral/calibration | Deadline-aware deferral | 6.4 / 6.6 | Backup |
| S — privacy | unlearning/fairness | P2 unlearning audit | 7.7 → 5.17 | Killed |
| M — stat/causal | causal/decision theory | M1 network sensitivity | 7.8 → 5.60 | Killed |
| M — stat/causal | causal/decision theory | M2 | 7.3 → 5.17 / 5.78 | **Killed by audit** |

The scans covered tokenization, compositional generalization, CoT identifiability, ICL, circuit minimality, grokking, data attribution, scaling laws, MARL, deferral, calibration, privacy, fairness, unlearning, causal sensitivity, experiment design, and reproducibility. Breadth is no longer the constraint.

---

## 18. Recommendation and next steps

### 18.1 Audit status

- **Trace:** **killed** by two independent audits. Theorem audit: 4.75 core / 5.50 weighted. Estimand audit: 4.08 core / 4.95 weighted. Do not run.
- **M2:** **killed** by the prior-art and sharpness audit. Strictness theorem false by nesting; joint set contains the marginal union; 60/60 sweep; LP only in a special case. 5.17 core / 5.78 weighted; 5.75 / 6.30 methods note only.
- **I1:** **killed** by the identification audit. Strategic theorem false under full joint support; Zhang 2026 pre-empts the identification criterion; reversal is support-driven. 5.75 core / 6.60 weighted; methods note only.
- **CBM:** on hold. The monitorability bound is not established; merge into Trace or drop.
- **No 7+ candidate remains.**

### 18.2 Decision rule

- If the team wants a Q1 paper: run one final 2–3 day audit on **duplication influence** (6.86 / 7.0, the last unaudited backup). If it passes at ≥6.5 core, execute it; if it fails, accept a 5.5–6.5 measurement note or bring in a theory collaborator.
- **Do not run Trace, M2, I1, or CBM as 7+ bets.**
- **Do not run another broad scan.**

### 18.3 Why Trace was killed

- The CC theorem is false for a centralized trace generator: equality with \(T=f(x)\) is a one-token counterexample, and a \((1,1)\)-BAPO solves equality/disjointness with constant bandwidth (Schnabel et al. 2025).
- Published length laws contradict the task predictions: parity and majority need \(\Theta(n)\) CoT steps (Amiri et al. 2025; Tomlinson et al. 2026).
- The estimand is undefined: no pair distribution, trace equivalence, computation label, or monitor class; training on input→trace→answer makes the trace faithful by construction.
- The checksum leaks the answer; the correct construction is a keyed random pointer, not a natural phenomenon.
- Closest: Mohammadkhani et al. 2026 (arXiv:2609.37312); Anwar et al. 2026 (arXiv:2602.18297); Meek et al. 2025 (arXiv:2510.27378).

### 18.4 If 8+ is non-negotiable

The realistic path is not another scan. It is one of:

1. a **theory collaborator** who can prove a sharp identification or minimax result in 2–3 months;
2. a **new dataset/intervention** (randomized tokenizer assignment, randomized advice, randomized deletion audits) that makes a standard estimand identifiable;
3. a **longer horizon** for a genuinely open identification problem.

Absent one of these, the honest target is a **6.7–7.3 core Q1 paper executed well**, not an 8+ label attached to a 5–6 idea.

---

## 19. Budget and feasibility summary

| Candidate | GPU-hours | CPU-hours | Storage | Cash | Timeline | Team fit |
|---|---:|---:|---:|---:|---|---|
| Trace Identifiability | — | — | — | — | — | **Killed; not run** |
| M2 | — | — | — | — | — | **Killed; 5.75 / 6.30 methods note only** |
| CBM | ~350 | ~50 | 25 GB | $0–10 | 6–7 months | LLM/interpretability; on hold |
| I1 protocol-relative credit | 0 | ~100 | 5 GB | $0–20 | 6–7 months | **Killed; 5.75 / 6.60 methods note** |
| Duplication influence | 400–600 | — | 50 GB | $0–30 | 6–7 months | **Best remaining; audit needed** |

**The $50 cap is not binding for the surviving options.** The binding constraints are theorem correctness, prior-art novelty, and experiment power.

---

## 20. Honest limitations

1. **CBM and duplication influence are scan scores, not verified novelty.** Trace, M2, and I1 were audited and killed. CBM’s bound is not established; duplication influence has not been audited.
2. **The literature coverage is not exhaustive.** “No paper defines X” claims are search-based, not proof of absence. The closest-paper check must be repeated for the final chosen project.
3. **Q1/APC status is not verified.** Verify the current JCR/Scopus quartile and APC policy for TACL, CL, JMLR, JRSS-B, Biometrics, and AIJ before submitting.
4. **The synthetic datasets are a strength and a weakness.** They give exact ground truth and CC/certificate labels, but they are not natural reasoning data. External validation is a robustness section, not the main claim.
5. **The weighted scores are close.** CBM 7.25, I1 7.25, duplication 7.0. Do not select a project on a small difference; select on theorem correctness, team fit, and the audit outcome.
6. **CBM’s earlier 7.8 was an arithmetic error.** The corrected 7.25 is below the original M2 and Trace scores; it should not be presented as the highest-weighted candidate.
7. **Trace’s 7.17 was optimistic.** Two audits revised it to 4.08–4.75 core. The CC theorem is false for a centralized trace generator, and the estimand is undefined.
8. **M2’s 7.3 was optimistic.** The audit revised it to 5.17 core. The strictness theorem is false by nesting: the joint set contains the marginal union, not a subset of the intersection. The 60/60 sweep is the decisive evidence.
9. **I1’s 7.07 was optimistic.** The audit revised it to 5.75 core. Zhang 2026 (*“Value Identifiable, Credit Not”*, AAMAS 2026) pre-empts the identification criterion; the strategic theorem is false under full joint support; the reversal is support-driven. The last 7+ backup is gone.

---

## 21. Source files (internal traceability)

| Topic | File |
|---|---|
| Final selection report | `FINAL_ROUND3_SELECTION.md` |
| Review package (earlier version) | `REVIEW_PACKAGE_TOP_IDEAS.md` |
| One-page decision memo | `DECISION_MEMO.md` |
| Short tracker | `TRACK.md` |
| Trace Identifiability scan | `archive/round3/14_learning_theory_scan.md` §4 |
| CBM scan | `archive/round3/17_llm_reasoning_scan.md` §2 |
| M2 scan | `archive/round3/15_stat_causal_theory_scan.md` §4 |
| Mergescope desk check | `archive/round3/22_desk_check_mergescope.md` |
| ReasonBound desk check | `archive/round3/23_desk_check_reasonbound.md` |
| P2 desk check | `archive/round3/25_desk_check_unlearning_audit.md` |
| M1 desk check | `archive/round3/27_desk_check_network_sensitivity.md` |
| Witness coverage desk check | `archive/round3/28_desk_check_witness_coverage.md` |

**This file is the single review copy.** The other files are working artifacts; they are listed only for traceability.
