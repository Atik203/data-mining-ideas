# Research Idea Tracker

**Purpose:** one short source of truth for what we explored, what survived, and why. No rigid C1/C2 labels. The goal is a **good Q1 paper within 6–7 months**, not a large portfolio of fashionable ideas.

**Single review copy:** `RESEARCH_SELECTION_PACKAGE.md` — all scores, datasets, rejected claims, and next steps in one self-contained file.

**Last updated:** 8 November 2026.

---

## 0. Current state — 8 November 2026

All six Round-10 gates are complete. The surviving-only ranking is:

| Rank | ID | Title | Core | Weighted | Decision |
|---:|---|---|---:|---:|---|
| 1 | E2 | **Tool-Observation Sensitivity in Language-Model Agents: A Fixed-Prefix Causal Study** | **7.38** | **7.55** | **GO, staged** — 800 real τ-bench prefixes, 3×7–8B, 5 arms; $63 base/$70 cap |
| 2 | RANKCAL | **The Ranking Cost of Instruction Tuning: Quality Beyond Accuracy and Calibration** | 6.75 | 7.18 | Conditional GO — 2,500 MMLU items + TOST ±.03 |
| 3 | R1 | **Causal Retrieval Lift: Separating Retrieved Evidence from Answer Presence** | 6.65 | 6.83 | Conditional GO — N=800 measurement paper |
| 4 | E1 | **Constraint Age or Constraint Load? Separating Two Causes of Multi-Turn Instruction Decay** | 6.63 | 7.10 | Redesign done — run the new 3-arm factorial |
| 5 | Tool-IV | **The Causal Effect of Tool Use on Answer Accuracy: A Randomized Encouragement Study** | 6.63 | 6.98 | Conditional GO after calibration |
| 6 | M1 | **Bangla Across Scripts: Native-to-Romanized Transfer and Contrastive Alignment** | 6.00 | 6.50 | Conditional — native audit then 9k/7B |
| 7 | T1 | **Watermark Survival Under Paraphrase: Surface Change, Not Semantic Distance** | 5.63 | 6.18 | Stop theory; measurement note only |

**No verified 8+ paper.** The best executable paper is E2; the best fallback is RANKCAL. T1’s mean-radius theorem was killed in Gate A, and Wang et al. (NeurIPS 2026) pre-empts the retained-signal threshold. Gate C passed E2 with an MDE of 0.20 logit/level and a measured supply of 6,554 eligible prefixes. Full detail: `round10/index.md`; evidence: `round9/FINAL_SURVIVORS_REVIEW.md`.

The green-computing track is separate: `green_computing/index.md`. **G3′ was killed by its pilot**; the provisional green lead is **G4′ (7.50/7.78)**, pending its pilot, with G2 (7.00/7.35) and G5′ (7.00/7.03) as fallbacks.

---

## 1. Selection principles

A candidate moves forward only if all five are true:

1. **Verified gap:** the five closest papers do not already contain the same estimand, assumption set, method, and guarantee.
2. **Q1 fit:** there is a credible Q1 journal/category where the contribution is important, not merely publishable.
3. **Decisive desk test:** the audit itself must identify one concrete experiment/analysis that would confirm or kill the claim; we do **not** run a 2-week pilot for every idea.
4. **Execution:** 3–4 people, 6–7 months, RTX 3060 6 GB, limited API/GPU budget.
5. **Portfolio independence:** not a superficial extension of FYDP, HaluRISC, or IntentGate.

**Practical score (0–10 each, simple average):** Gap evidence · Q1 fit · Decisive desk test · Execution · Independence.  
The detailed 8-parameter audit remains an appendix; this five-point screen is what we use to decide. A short 3–5 day check is allowed only if two ideas are tied on one decisive ambiguity.

---

## 2. What we explored

| Idea (short name) | Domain | Closest verified competitor | Why it did not move forward | Decision |
|---|---|---|---|---|
| LLM-agent counterfactual evaluation | LLM agents / OPE | ADWM (NeurIPS 2026); Chae et al. (2026); Wei et al. (ICML 2026); Zhang–Jiang hardness | Core framing occupied; budget component published; no propensity logs; natural theorem blocked | Reject |
| MNAR software data fusion | statistics / SE | PPI-IPW; RePPI; Kluger; Seaman; Mozer | Design-based/PPI solves identification; MNAR historical data adds only efficiency | Reject as method; empirical fallback only |
| Audit-budgeted selective learning | active inference / auditing | SIV (ICLR 2027 submission); AM-PPI; Das et al.; Lindon–Kallus | Joint formulation occupied; theorem is a composition; no real audit data | Reject standalone |
| Nonstationary causal discovery | causal discovery | CANMI; ReTimeCausal; Chen et al.; Ceriscioli et al. | Exact joint formulation occupied; general problem non-identifiable | Reject |
| Dependency-upgrade spillovers | causal inference / SE | Imai–Jiang–Malani; Hoshino–Yanagi; Shankar et al.; Schröder et al. | Method is a composition; real empirical gap remains | **Conditional empirical** (EMSE/IST) |
| Agent reliability under dependence | agent evaluation | Tironi et al. (2026); Singh & Singh (2026); Buran & Čadek (2026) | Exact estimator and moment boundary published before cutoff | Reject |
| AI code review → verified repair | SE / human–AI | Tufano (ICSE 2025); Sun (TSE 2026); Meta/Google RCTs | Narrow empirical gap, no method novelty; field RCT needs a partner | Partner-only |
| Security advisories → upgrade/breakage | SE / security | Kula 2018; Rahman 2026; Hasan 2026; Fischer CCS 2023 | Descriptive literature saturated; advisory timing endogenous | Reject / merge |
| Supply-chain isolation policies | security / networks | He et al. FSE 2025; Wang 2026; Schueller & Wachs 2024 | Policy simulation occupied; percolation framing wrong; no ground truth | Reject as method |

---

## 3. Round 3 — outcome of the five audited ideas

All five are **closed for an 8+ methods paper**. Scores are adversarial, not aspirational.

| Idea (short name) | Core question | Weighted / core | Why it failed | Best surviving version |
|---|---|---:|---|---|
| Mechanistic causal validity | When does an internal explanation capture a mechanism that generalizes to unseen compositions? | 5.40 / 5.14 | Estimand is a CATE; compositional identification is transportability; criterion/estimator published (Felekis 2026; Bukkapatnam–Karuturi 2026; Asiaee 2026) | Compositional-holdout benchmark; 5–6, TACL/NeurIPS D&B |
| Scientific experiment design | How should an ML-engineering agent select the next experiment under compute and implementation uncertainty? | 5.05 / 4.78 (narrow 5.70 / 5.50) | Cost-aware MCTS + implementation variance already published (MARS; The Implementation Lottery; ABE-Ralph; DCP) | Implementation-robust experiment selection; 5.5–6.5 |
| Experience reuse under drift | When should an agent reuse, adapt, verify, or discard past experience after tools/environments change? | 4.83 / 4.56 | Decision-theoretic core and no-regret guarantee published (CASCADE; Causal Memory Policy; ToolChain-CRC) | Measurement under semantic drift |
| Reliable agent evaluation | Can a protocol jointly control contamination, nondeterminism, and cost? | 5.00 / 4.97 | Protocol integration only; contamination correction, audit power, HARP already cover the pieces | Protocol/measurement |
| Reproducibility prediction | Can we distinguish execution failure, implementation mismatch, and genuine non-reproducibility? | 5.48 / 5.28 (reframed 5.83) | ReproScore, AI Reproducibility Index, ICML 2026 Open Reproductions, ReproRepo pre-empt the space | Label-reliability measurement; 5.8 |

**Both 8+ theory desk checks also failed:**

| Desk check | Verdict | Why |
|---|---|---|
| Restricted-alignment compositional abstraction | **False as stated**; 5.05 / 4.30 | Restriction constrains the alignment, not the model; a context-gated mechanism preserves atomic IIA but makes composite IIA arbitrary. Repaired version assumes away the question. |
| Minimax experiment design under silent failures | **No new theorem**; 5.18 / 4.86 | Reduces to cost-aware best-arm identification with biased feedback (Jedra et al., EWRL 2026; CABAI; NHSHT; multi-fidelity/correlated BAI). |

**Decision rule going forward:** do not force an 8+. A candidate must either produce a genuinely new estimand/identification result/minimax theorem, or be selected honestly as a 6.5–7.3 empirical/measurement paper.

---

## 4. Current frontier — what survived and what died

The search converged on tokenization, CoT mediation, unlearning audits, and network sensitivity. **All four 8+ claims failed their desk checks.** The survivors are measurement papers and lower-ceiling theory papers, not 8+ methods.

| Candidate | Core claim | Provisional core | Budget | Desk check | Status |
|---|---|---:|---:|---|---|
| **Mergescope** (low-budget scan) | Shapley value of each merge on OOD compositional accuracy; identification on a pairwise-interaction merge forest; \(\Omega(K2^\tau)\) interventions; \(\tau\) = treewidth | **KILLED** (5.81 core / 6.08 weighted) | — | **Desk check failed:** estimand needs a precedence convention; Theorem 1 is a two-line identity; Theorem 2 is parameter counting and false for dense pairwise interactions; ICC gate underpowered. Closest: Lesci et al. 2025 (ACL) causal subword inclusion; Sälevä–Lignos 2023 random merges show little downstream change. Keep only a 6.0–6.5 measurement pilot. |
| **Merge-boundary interference** (learning-theory scan) | Merge crossing a morpheme boundary has a nonzero causal effect not identified by matched vocabulary/compression/frequency; boundary merging forces \(\Omega(|S|)\) samples vs \(O(\sqrt{\log/n})\) when preserved | **Downgraded to 6.5–7.0** | $0–20; 150–200 GPU-h; 50–80 GB | Shares Mergescope's premise and the same threats: Lesci et al. already estimate subword-inclusion effects; the separation is model-class dependent; random-merge evidence suggests effects are small. Not an 8+. |
| **ReasonBound** | Token-level CoT mediation; claimed sharp Fréchet–Hoeffding bounds under an intervention exclusion restriction; \(\sqrt{n}\) plugin; minimax lower bound | **KILLED** (5.11 core / 5.43 weighted) | — | **Desk check failed:** estimand conflates controlled, interventional and natural effects; the LP is Miles et al. 2017; the exclusion restriction does not solve cross-world confounding; the 30%-narrow gate is unattainable; full compute/annotation underbudgeted. Closest: Paul et al. 2024; Macar et al. 2026; Somov et al. 2026. |
| **P2 — unlearning audit limits** | Deletion signal is bounded by \(\kappa(z)/n\); audit cost was claimed as \(mq \gtrsim n^2/(\kappa^2\Delta^2)\); matching paired test. | **KILLED** (5.17 core / 5.48 weighted) | — | **Desk check failed:** influence bound survives but is standard; the audit-cost exponent is off by \(n\) (unpaired) or \(n^2\) (paired); \(q\) queries are not \(q\) samples; the auditability threshold is inverted. Exact OLS: paired cost \(O(1)\), unpaired \(\approx2.3n\), not \(10^{10}\). Survivor: 6.0–6.5 note *“Queries Buy Direction, Not Samples.”* |
| **I1 — protocol-relative credit** | Credit assignment is a functional of the intervention protocol; point identification iff positivity; strategic protocols are not identified even with full support; sharp LP bounds | 7.25 | $0–20; CPU only; 5 GB | Reversal rate ≥5% and LP width ≤0.9 of reward range; otherwise kill | **Desk check not started; backup** |
| **Witness coverage** (NLP scan) | Average morphological alignment is not sufficient for OOD composition; item-level witness coverage is; matched tokenizers can differ in OOD accuracy predicted by coverage profiles | **KILLED** (5.72 core / 6.0 weighted) | — | **Desk check failed:** coverage is collinear with MorphScore (Pearson .782, Spearman .880); zero strict matched pairs under exact vocabulary, \(|\Delta A|\le.05\), fertility 0.9–1.1, \(|\Delta O|>.2\). Closest: Coverage Principle 2505.20278 and MorphScore 2507.06378. Only a ~6.0 test-weighted exact-match measurement paper survives. |
| **M1 — network sensitivity** | Sharp joint bounds for direct and spillover effects under unmeasured network confounding; network robustness value \(\Gamma^\ast\); polygon, not a rectangle | **KILLED** (5.60 core / 6.33 weighted) | — | **Desk check failed:** the marginal NMSM contains the point-treatment MSM as a special case, so the direct-effect sharp set *contains* the Dorn–Guo–Kallus bound, not a strict subset; the joint polygon is a generic zonotope from a shared control outcome. Only a 5.5–6.5 negative/measurement note survives. |
| **M2 — joint confounding + misclassification** | Sharp joint bounds for the ATE when treatment is both confounded and misclassified; bias-compensation frontier \(B(\Gamma,\delta)\) | 7.3 | $0–30; 20–40 GPU-h; 10 GB | Joint bound must be strictly narrower than the intersection of marginals; Díaz–van der Laan 2013 may pre-empt | **Backup** |

**Crowding warning (NLP scan):** TokSuite (2512.20757, ICML 2026) trains 14 models identical except for the tokenizer; *Tokenization Is More Than Compression* (2402.18376, EMNLP 2024) trains 64 models across tokenization choices; *Tokenization Constraints* (2505.14178) formalizes token awareness; the Coverage Principle (2505.20278) gives a sample-complexity account. The "does tokenization matter" question is dead. Only item-level mechanism/attribution survives, and that is a 7–7.6 measurement contribution unless a new theorem is proved.

**Verdict after nine scans, nine desk checks, and Rounds 4–7 (5 November 2026): no verified 8+ idea exists, and every 7+ candidate has been killed or is unverified.** All eight original high-ceiling claims failed under adversarial checking: restricted-alignment abstraction (4.30), minimax experiment design (4.86), Mergescope (5.81), ReasonBound (5.11), P2 unlearning audit (5.17), M1 network sensitivity (5.60), witness coverage (5.72), and Trace Identifiability (4.08–4.75). **M2 was killed (5.17 / 5.78): the strictness theorem is false by nesting. I1 was killed (5.75 / 6.60): Zhang 2026 pre-empts the identification criterion, the strategic theorem is false under full joint support, and the reversal is support-driven.** **Round 4 then audited five fresh non-hallucination directions (I1 script effect, I2 code-switched CoT, I3 multilingual safety, I4 calibration tax, I5 ICL influence) and three contribution types (T1 identification, T2 lower bound, T3 controlled dataset); all failed.** **Round 5 killed the style-calibration theory (model/lemma 4.45, Pareto 4.45, prior art 5.5; prompt-based pilot 7.0). Round 6 killed the three decision-first pre-flights (A evidence budget 4.45, B adaptive contamination 4.00, C calibration transfer 4.60). Round 7 killed the PT→IT frontier (theory 4.75, experiment 5.9) and failed the StyleCal Pythia pilot (6.15): chance-level MMLU, letter collapse, matched-prefix ΔECE +0.0089/−0.0317.** The Qwen2.5-0.5B-Instruct pilot also failed: base 42.6%, but wrapper removal collapsed the style models to 26.3%/28.7%; in-distribution ΔECE -0.045 (wrong sign), style arms indistinguishable. **StyleCal-10k is dead.** No fresh executable remains. No 7+ candidate remains. The last unaudited backup is **duplication influence (6.86 / 7.0)**; CBM (6.7 / 7.25) is on hold. Do not force an 8+ label onto a 6.7 paper. Single review copy: `RESEARCH_SELECTION_PACKAGE.md`; Round 4 synthesis: `archive/round4/09_synthesis.md`.

**What the convergence taught us:** three scans converged on tokenization, but the desk check showed the closest causal paper (Lesci et al. 2025, ACL) already estimates subword-inclusion effects and the claimed Shapley theorem is trivial or false. Convergence is a signal to check the literature, not evidence of novelty.

**What remains:** no 7+ paper. Trace (4.08–4.75), M2 (5.17), I1 (5.75), and all five Round 4 ideas are dead; the 7.6 witness candidate is dead. The best fresh executable is **I4 + StyleCal-10k (6.17 / 6.55)**; the last unaudited backup is **duplication influence (6.86 / 7.0)**; CBM (6.7 / 7.25) is on hold. If the team wants the highest score, audit duplication; if it wants a fresh paper in its own skills, execute StyleCal-10k. If 8+ is non-negotiable, bring a theory collaborator for the PT→IT frontier theory that Hu et al. (2510.17426) explicitly leave open. Do not run another scan without a new theorem hypothesis. Single review copy: `RESEARCH_SELECTION_PACKAGE.md`; Round 4 synthesis: `archive/round4/09_synthesis.md`.

---

## 5. What “good Q1” means here

A Q1 paper in 6–7 months is more likely if the contribution is one of:

- a **new estimand/identification result** with a decisive empirical test;
- a **new method** with a guarantee that existing baselines cannot match;
- a **measurement result** that changes how the field interprets a widely used benchmark;
- a **dataset/benchmark** that enables a scientific claim nobody could test before.

### Latest surviving-idea review — 8 November 2026

Use `research_audit/round9/FINAL_SURVIVORS_REVIEW.md` for the current surviving-only table. It independently rechecks the pilot ledgers and corrects the Round-8 scan scores. Main corrections: E1's λ is confounded with active constraint load once turn fixed effects are added; T1 validates a white-box plant but not its minimax phase boundary; R1 has a strong retrieval-lift estimate but its twin/contamination extension fails; E2 is a small-n screening signal. **No survivor is a proven 8+ paper yet.**

It is less likely if the contribution is:

- a new application of a known method;
- a combination of known methods with no new guarantee;
- a benchmark with no new scientific question;
- a “framework” or “protocol” that is a checklist rather than a result.

---

## 6. Archive

The bulky per-candidate reports, ledgers, PDFs, and probe files from the rejected rounds have been moved to:

`C:\Users\atiku\AppData\Local\Temp\opencode\research_audit_archive_2026-11-05`

The current Round-3 working files remain in `research_audit/archive/round3/` until the audit is consolidated.

**Rule going forward:** keep only this tracker, the active round’s brief/reports, and the final selection report. Do not keep a growing library of rejected candidate files in the workspace.
