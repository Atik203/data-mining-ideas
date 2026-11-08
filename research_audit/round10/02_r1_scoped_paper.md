# Round 10 — Task B: R1 scoped-paper gate

**Date:** 8 November 2026  
**Question:** Can R1 survive as a paper after removing the contamination, twin/conflict, and theorem claims, and if so, what exactly does N=800 buy?  
**Inputs:** `round10/00_BRIEF.md`; `round9/pilot_idea8_realcorpus.md`; `round9/08_idea8_ablaterag.md`; `round9/ledger_P_I08b.csv`; `round9/prep_i08b.json`; `round9/pilot_i08b_stats_final.json`; `round9/run_pilot_i08b.py`; `round9/run_pilot_i08b_twin2.py`; `round8/04_rag_eval_scan.md`; `round9/FINAL_SURVIVORS_REVIEW.md`.  
**Rules:** CPU only. No pilot re-run, no GPU, no paid API. Literature checked by keyless search/fetch; abstracts verified, with targeted full-text probes where noted.

---

## 0. Bottom line

| Item | Decision |
|---|---|
| Paper | **CONDITIONAL GO** as a bounded measurement paper (Findings/TACL class), **not** an 8+ novelty paper. Adjusted Core ≈ 6.7, Weighted ≈ 6.8; the drop from 7.38 is the price of removing invalid claims. |
| Claim set | Randomized-order retrieval lift (`τ`), retrieval-dependent success share (`AF`), answer-presence-conditioned lift (`τ_present`, `τ_absent`), random-passage penalty (`δ`). No contamination bound, no twins/conflict, no Manski/minimax theorem. |
| Closest paper | **Maekawa et al. 2024, “Retrieval Helps or Hurts?” (NAACL 2024)** is closest in question. **Amiraz et al. 2025, “The Distracting Effect” (ACL 2025)** is the nearest descriptive ablation. AttriBoT/ContextCite are the nearest attribution methods; Sela 2026 is the nearest contamination test; Chen et al. 2026 is the nearest conflict paper. |
| Twin decision | **Drop the conflict claim from R1.** The pilot’s 74% is model compliance, not construction validity; the v2 twins are semantically type/relation-incoherent (no NER matching); the twin rows were gold-only, so `M = C(X) − C(X')` was never identified. The closest conflict paper already reports a 92% valid conflict-generation rate (including Entity Swap). A valid protocol is specified in §4 but is a separate study, not recommended now. |
| N=800 | **MDE₈₀ = 5.5pp** for `τ`, **2.6pp** for `δ`, **7.7pp** for the answer-presence interaction; 95% CI half-width **3.9pp** for `τ`. Pilot effects are 32.5pp, −7.5pp, 77.8pp, so the study is well powered; the binding risks are 7B replication and prompt asymmetry, not sample size. |
| Verdict | **CONDITIONAL GO** for the measurement paper; **NO-GO** for conflict, twin, contamination, Manski, and minimax claims. Stop if the 100-item symmetric-prompt check gives `τ < 10pp`, or the full N=800 run gives `τ < 5pp` or no answer-presence interaction. |

---

## 1. What the pilot actually identified

### 1.1 Recomputed pilot facts

I recomputed the 700-row ledger (`ledger_P_I08b.csv`) rather than trusting the summary. There are 600 base rows (200 questions × 3 arms) and 100 twin rows (50 v1 + 50 v2); the 50 v1 twins are excluded from the verdict, as the pilot says.

| Quantity | Value | Note |
|---|---:|---|
| Top-1 accuracy | 0.400 | called “gold” in the pilot, but it is BM25 top-1, not the dataset gold paragraph |
| Shuffled accuracy | 0.000 | random non-answer passage |
| Empty accuracy | 0.075 | no-retrieval baseline; **not** a contamination estimate |
| `τ` (top-1 − empty) | +0.325, SE 0.0394, 95% CI [0.2477, 0.4023] | paired within query |
| `δ` (shuffled − empty) | −0.075, SE 0.0187, 95% CI [−0.1116, −0.0384] | paired within query |
| Paragraph hit | 104/200 (52.0%) | top-1 passage is the dataset gold paragraph |
| Answer recall | 109/200 (54.5%) | an answer alias occurs in top-1 |
| P(EM \| answer present) | 80/109 = 73.4% | top-1 arm |
| P(EM \| answer absent) | 0/91 = 0% | top-1 arm; exact 95% upper bound ≈ 4.0% (Clopper–Pearson) |
| `τ_present` | +0.679, SE 0.045 | top-1 − empty, answer-present subgroup |
| `τ_absent` | −0.099, SE 0.031 | top-1 − empty, answer-absent subgroup |
| Per-stratum `τ` range | 0.241–0.448 | all 7 question-type strata positive in the pilot |
| Interaction `τ_present − τ_absent` | +0.778 | descriptive; not pre-registered as a formal interaction in the pilot |
| Retrieval-dependent success share `AF = τ / E[Y(top-1)]` | 0.8125 | bootstrap 95% CI [0.704, 0.901] at N=200 |
| Twin v1 EM | 14/50 = 28% | v1 random cross-type donor; discarded |
| Twin v2 strict EM | 26/50 = 52% | model produced exactly the new answer |
| Twin v2 contains-new | 37/50 = 74% | model response contained the new answer |
| Errors | 0/700 | stable Ollama run |

The exact decomposition is the law of total expectation:

```
τ = π · τ_present + (1 − π) · τ_absent
  = 0.545 · 0.679 + 0.455 · (−0.099)
  = 0.370 − 0.045
  = 0.325
```

This is not a structural model. It is an exact descriptive decomposition of the paired treatment effect by a deterministic feature of the treatment passage (answer present/absent). It is the paper’s cleanest result.

### 1.2 Six corrections that must appear in the paper

1. **“Gold” is top-1, not gold.** Only 104/200 top-1 passages are the dataset gold paragraph; 96/200 are not. The paper must rename the arm “top-1 retrieved passage” and report paragraph hit and answer recall. The pilot’s `acc_gold = 0.40` is top-1 accuracy, not gold-paragraph accuracy.
2. **“Randomized arms” is randomized order.** Every query receives all three arms; the order is shuffled. This is a within-query repeated-measures design with randomized order, not random assignment to treatment. The paper must say “paired retrieval ablation with randomized order” and “within-query causal contrast.” It should not call itself a randomized controlled trial.
3. **`C = 0.075` is no-retrieval accuracy, not a contamination bound.** It contains parametric knowledge, reasoning, guessing, and task priors. The pilot’s claim that `C` upper-bounds contamination-driven correctness is invalid. The paper may use `C` as the baseline in `τ` but must not interpret it as contamination.
4. **Twin “validity 74%” is compliance, not construction validity.** The v2 construction replaced only the answer string in the gold paragraph, used another SQuAD answer as donor, matched question-word stratum and length but **not NER type**, and left co-referents and identifying cues in place. Examples: “Friedrich Ratzel of Scandinavia” (birthplace answer reused as a descriptor; relation mismatch), “the exam at the end of Form Four = Flung to the Heedless Winds” (exam vs poem title), “ABC launched = stronger” (service vs adjective). The build did check new-entity presence and old-answer absence, but it did not run a type-match/relation/coherence/two-rater validity audit. The 74% is the model’s willingness to output the new string; a valid twin can have low compliance (parametric override) and still be valid, and an invalid twin can have high compliance (copy the string). The pre-registered kill rule conflated the two.
5. **The twin design never identified `M`.** The twin rows were `arm = gold` only; there was no empty arm for the twin question. `M = C(X) − C(X')` requires the twin question under both empty and twin passage, plus the base question under both. The pilot measured `P(new answer | twin passage)`, not a memorization gap. Even a perfectly constructed twin could not rescue `M` from this design.
6. **Prompt asymmetry may inflate `τ` and `δ`.** The context arms used “Use only the context below to answer”; the empty arm used only “Question: … Answer with only the exact answer text.” The context arms were explicitly instructed to use the passage; the empty arm was not. This is a confound. The N=800 design must use a symmetric instruction (“Use the context if provided; otherwise answer from your knowledge. Context: {passage or (none)}”) and a 100-item symmetric-prompt check before the full run. The pilot’s effect may shrink.

Corrections 1–3 are labeling/interpretation; 4–6 are design flaws. All are fixable in the scoped paper except the twin/conflict claim, which is dropped.

---

## 2. Claim set rewrite

### 2.1 Confirmatory claims

| ID | Claim | Estimand | Design / evidence | Status |
|---|---|---|---|---|
| **C1** | On real Wikipedia passages, the retriever’s top-1 passage raises exact-match QA accuracy relative to no retrieval. | `τ = E[Y(T) − Y(E)]` | Paired within query; N=800; 3 open-weight models; BM25 + dense retriever; pilot +32.5pp [24.8, 40.2] | Confirmatory, primary |
| **C2** | The lift is concentrated where the top-1 passage contains the answer; when the answer is absent, top-1 retrieval does not help and can hurt. | `τ_present`, `τ_absent`, interaction `τ_present − τ_absent` | Deterministic answer-in-passage split; pre-registered interaction; pilot +67.9pp vs −9.9pp, interaction +77.8pp | Confirmatory, key mechanistic |
| **C3** | A random non-answer passage is not a neutral substitute for no retrieval; it reduces accuracy in this setup. | `δ = E[Y(S) − Y(E)]` | Paired within query; pilot −7.5pp [−11.2, −3.8]; N=800 MDE 2.6pp | Confirmatory, secondary |
| **C4** | A substantial share of top-1 successes is retrieval-dependent. | `AF = τ / E[Y(T)]` | Ratio estimator, bootstrap CI; pilot 81.3% [70.4, 90.1] | Confirmatory, secondary |
| **C5** | The pattern is directionally robust across question types, retrievers, and model families. | `τ_s`, `τ_r`, `τ_m` | Descriptive forest plots; no scale law; per-stratum N=800 MDE 14.6pp | Exploratory |

`Y_i(a)` is exact match for query `i` under arm `a ∈ {E, S, T, O}`: empty, random non-answer, top-1, oracle gold paragraph. `π = P(answer in T)`. `τ_present = E[Y(T) − Y(E) | answer in T]`; `τ_absent = E[Y(T) − Y(E) | answer absent]`. `AF` is the share of top-1 correct answers that would not be correct without retrieval; it is **not** a contamination rate.

### 2.2 Recommended fourth arm (oracle gold)

Add `O = ` the dataset’s gold paragraph. It is not a randomized treatment; it is a benchmark upper bound on what retrieval could deliver. The oracle gap `E[Y(O) − Y(T)]` quantifies the retrieval-quality gap and calibrates how much of the oracle lift the retriever captures; it directly answers the “top-1 is not gold” objection. The primary estimand remains `T − E`; the oracle arm is secondary. With N=800, 4 arms × 3 models × 800 = 9,600 calls for BM25, plus a dense-retriever replication of `T/E`, about 14,400 calls total — still cheap.

### 2.3 Claims removed and why

| Removed claim | Reason |
|---|---|
| Contamination bound `C = E[Y(empty)]` | `C` conflates contamination with reasoning, guessing, and task priors; no identification. Report it as no-retrieval accuracy only. |
| Memorization gap `M = C(X) − C(X')` | Twins invalid; twin rows gold-only; `M` not identified even with valid twins in this design. |
| Entity-swapped conflict robustness | v2 twins not NER-type-matched; 74% is compliance; conflict literature already crowded (Chen et al. 2026 reports a 92% valid conflict-generation rate across its perturbation subgroups). |
| Manski sharp bounds / monotone A1 (`A1`: gold weakly dominates empty per stratum) | Not needed for the randomized contrast; `A1` was a pilot check, not an assumption. Keep only a descriptive “all strata positive” check. |
| Minimax `Ω(1/π²)` detection rate | Unproved and irrelevant once the contamination claim is dropped. |
| “First randomized retrieval ablation” | Cannot be supported by this search; use “we are not aware of a within-query paired ablation that reports this decomposition with pre-registered inference.” |
| “Distraction” as a general law | The shuffled arm is a random corpus passage, not a selected hard distractor; call it a “random-passage penalty” and report passage characteristics. |

### 2.4 Identification statement for the paper

Under SUTVA, consistency, and no carryover, `τ` is identified by the within-query paired difference. Order is randomized to support no carryover; treatment levels are fixed and fully administered. No monotonicity assumption is needed. The answer-presence split is a deterministic feature of the realized treatment passage; `τ_present` and `τ_absent` are conditional average effects, not structural mediation parameters. `AF` is a ratio of the paired effect to top-1 accuracy; it is descriptive and must not be read as contamination.

---

## 3. Literature recheck: five closest functional papers

### 3.1 Method

Keyless search and fetch only (no paid API). I verified the abstract page for each paper and ran targeted full-text probes where the abstract left the design ambiguous. Verification level is stated per row.

| # | Paper | What it does | Design / inference | Delta vs R1 | Verification |
|---|---|---|---|---|---|
| 1 | **Amiraz, Cuconasu, Filice, Karnin. “The Distracting Effect: Understanding Irrelevant Passages in RAG.” ACL 2025 (2025.acl-long.892); arXiv:2505.06914.** | Defines a quantifiable distracting effect of an irrelevant passage; identifies hard distractors; fine-tunes on them; up to 7.5% accuracy gain. | Descriptive passage-level score; no empty-context arm reported; no randomized within-query arms reported; no population estimand/CIs. The HTML notes prior work (Cuconasu et al. 2024) found random passages did not degrade quality, while selected distractors did. | R1 estimates a paired population lift with CIs and conditions on answer presence; the random-passage arm is a baseline, not a selected hard distractor. | Abstract verified; HTML probe for “random”/“empty” (no “empty” hit). |
| 2 | **Maekawa, Iso, Gurajada, Bhutani. “Retrieval Helps or Hurts? A Deeper Dive into the Efficacy of Retrieval Augmentation to Language Models.” NAACL 2024 (2024.naacl-long.308); arXiv:2402.13492.** | Builds WiTQA; fact-centric analysis of entity/relation popularity; diverse LMs/retrievers; shows retrieval does not consistently help; adaptive retrieval. | Descriptive comparisons with/without retrieval; no within-query randomized arms reported; no paired population estimand; no pre-registered inference. | R1’s paired ablation identifies the causal contribution of the retrieved passage and decomposes it by answer presence. | Abstract verified; HTML probes for “without retrieval” (1 hit), “confidence” (no hit). |
| 3 | **Joren, Zhang, Ferng, Juan, Taly, Rashtchian. “Sufficient Context: A New Lens on Retrieval Augmented Generation Systems.” ICLR 2025; arXiv:2411.06037.** | Introduces sufficient-context classification; stratifies errors; larger models answer when context is sufficient but do not abstain when insufficient; selective generation. | Descriptive sufficiency classification; no randomized retrieval arms reported; no empty arm reported; no paired population estimand. | R1 randomizes the passage condition and estimates lift; answer presence is a deterministic treatment feature, not a model-based sufficiency judgment. | Abstract verified. |
| 4 | **Liu, Kandpal, Raffel. “AttriBoT: A Bag of Tricks for Efficiently Approximating Leave-One-Out Context Attribution.” ICLR 2025; arXiv:2411.15102. Cohen-Wang, Shah, Georgiev, Madry. “ContextCite: Attributing Model Generation to Context.” arXiv:2409.00729.** | LOO context attribution; per-span influence of context on a generation; efficient approximations; applications to verification, pruning, poison detection. | Per-instance attribution via context ablations; no population average treatment effect; no randomized arm assignment; no sampling CIs for a retrieval lift. | R1 estimates a population-level paired effect, not a per-instance attribution. The object and inference differ. | Abstracts verified; AttriBoT HTML probe for “confidence interval” (no hit). |
| 5 | **Sela. “No Memorization, No Detection: Output Distribution-Based Contamination Detection in Small Language Models.” arXiv:2603.03203 (2026).** | CDD contamination detector via output peakedness; small LMs 70M–410M; often at chance even when data is verifiably contaminated; perplexity/Min-k outperform. | No retrieval conditioning; no empty-context ablation; detection is from output distributions, not randomized retrieval. | R1 **drops** the contamination claim; `C` is no-retrieval accuracy, not a contamination estimate. This is a reason to narrow, not a competing bound. | Abstract verified. |

**Additional checked (not in the five, but directly relevant):**

- **Chen et al. 2026, “Does RAG Know When Retrieval Is Wrong? Diagnosing Context Compliance under Knowledge Conflict,” arXiv:2605.14473v4.** Context-Driven Decomposition; Entity Swap, Temporal Shift, Logical Contradiction, Distractor Evidence; closed APIs; “manually audited a random sample of 50 adversarial records per subgroup, finding a 92% valid conflict generation rate” (200 records total; no inter-annotator agreement; per-record labels not retained). **This occupies the conflict/twin claim** and sets a 92% validity bar.
- **Sun, Bai, Dredze. “Task Matters: Knowledge Requirements Shape LLM Responses to Context–Memory Conflict.” Findings of ACL 2026, pp. 4154–4176; 2026.findings-acl.202.** Controlled conflicts across tasks with different knowledge requirements; descriptive diagnostic; no randomized retrieval ablation. **Conflict claim occupied.**
- **Peng et al. 2026, “Navigating Unreliable Parametric and Contextual Knowledge: Explicit Knowledge Conflict Resolution for LLM Inference,” arXiv:2606.20245.** MACR multi-agent conflict-resolution method; no causal ablation. **Method paper, not a measurement competitor.**
- **Shaikh 2026, “Dissecting Agentic RAG: A Component Ablation for Multi-Hop QA with a Local 7B Model,” arXiv:2606.21553.** Eight-condition component ablation on 5,000 HotpotQA questions with Qwen2.5-7B; descriptive pipeline comparison; no empty-context population estimand. **Shows the ablation space is crowded; R1 must stay at the estimand level, not “we ablate components.”**
- **Huang et al. 2026, “Parameters vs. Context: TRACE Fine-Tuning for Robust RAG,” arXiv:2609.30337 (ICDM 2026).** Conflict-robust fine-tuning; method paper; not a causal ablation. **Not a competitor for the scoped claim.**

### 3.2 Delta confirmation

**No direct preemption found in the checked sources.** I did not find a paper that (i) administers top-1, random non-answer, and empty retrieval within the same query with randomized order, (ii) estimates a paired population lift with CIs, and (iii) decomposes the lift by answer presence. The closest papers are descriptive (Maekawa, Amiraz, Joren), attribution methods (AttriBoT, ContextCite), or contamination detectors (Sela). The delta is real.

**But the delta is measurement-level, not phenomenal.** Maekawa already shows retrieval does not always help; Amiraz already quantifies passage distraction; Joren already shows context sufficiency matters; AttriBoT/ContextCite already ablate context. R1’s contribution is the **paired population estimand + answer-presence decomposition + pre-registered inference**, not the discovery that retrieval helps. The paper must be positioned that way or it will be rejected as a replication.

**Conflict and contamination claims are not salvageable.** The conflict space has a 92%-valid conflict-perturbation study (Chen et al. 2026; manual audit, 50 per subgroup, including Entity Swap), a task-conditioned conflict study (Sun et al. 2026), and a conflict-resolution method (Peng et al. 2026). The contamination space has CDD and related detectors; R1’s `C` is not a detector. Dropping these claims costs the paper its most novel-sounding contribution. That is the honest price of validity.

---

## 4. Twin construction: design and decision

### 4.1 Why the pilot’s twins fail

The v2 construction is not NER-type-matched. It matched:

- question-word stratum (`who/what/when/where/which/how/other`),
- answer length within ±2 words,
- numeric vs non-numeric status.

It did **not** match entity type. It replaced only the first answer string in the gold paragraph, left other mentions and identifying cues, used another SQuAD answer (a real entity with possible parametric support) as donor, and kept the question unchanged (the pilot reports `q_swapped = 0`). The examples in the v2 ledger show relation/type mismatches: a birthplace answer reused as a descriptor, an exam answer replaced by a poem title, and a service answer replaced by an adjective. The passages are often incoherent.

The 74% contains-new rate is **model compliance**, not construction validity. The pilot’s kill rule (“twin validity <90%”) was therefore applied to the wrong quantity. A valid twin can produce low compliance because the model overrides the passage with parametric knowledge — that is the conflict effect of interest, not a construction failure. Conversely, an invalid twin can produce high compliance if the model simply copies the new string. The pilot checked new-entity presence and old-answer absence at build time, but it did not run a construction-validity audit (NER type match, coherence, two raters).

Even with a perfect twin, `M = C(X) − C(X')` was not identified: the twin rows were `arm = gold` only. There was no empty arm for the twin question, and no base/twin cross. The pilot measured `P(new | twin passage)`, not a memorization gap.

### 4.2 A valid NER type-matched protocol (if pursued)

This protocol is designable but **not recommended for R1**. It is included so the conflict claim is not dropped for lack of a design.

**Eligibility.** Base item `(Q, P, A)` with answer type `T ∈ {PERSON, ORG, GPE, DATE, NUMBER}`. Require:

1. Two NER systems (e.g., spaCy and Stanza) agree on `T` for the answer span in `P`; disagreements dropped.
2. `Q` does not contain `A` or any alias of `A` (the pilot reports `q_swapped = 0` for its sampled twins; verify per item).
3. `A` is a single mention, not a pronoun or multi-entity span.
4. The answer sentence contains exactly one entity of type `T` that satisfies the question relation; other type-`T` entities in `P` are either absent or clearly not answers.
5. A complete mention inventory of `A` in `P` can be built: exact string, normalized string, aliases, titles/appositives, demonyms, and coreferent nominals. If the inventory is incomplete, drop.

**Donor.** Generate a fictitious `A'` of type `T`:

- PERSON: syllable-recombined given name + surname from a phonotactic inventory.
- ORG: invented name with a type-appropriate suffix.
- GPE: invented place name with a type-appropriate suffix.
- DATE: shift year/month/day, preserving format and granularity.
- NUMBER: perturb by a factor in [0.6, 1.5], preserving format, units, and magnitude.

Verify `A'` is absent from Wikipedia/Wikidata and from a gazetteer (for GPE/ORG/PERSON). Later, verify the model does not produce `A'` with no passage (0/5 samples); if it does, regenerate — that is a parametric-support check, not construction validity.

**Document rewrite.** Replace every mention in the inventory with `A'` or a type-consistent alias, preserving grammar, number, and definiteness. Replace identifying appositives/titles with type-consistent generic descriptions. Remove or alter event/date cues that uniquely identify `A` (e.g., if `A` is the winner of a named event, replace the event name or neutralize the sentence). Keep all non-identifying content to preserve reasoning shape.

**Leak audit.** Search `P'` for `A`, all aliases, and any 4-gram overlap with `A`’s Wikipedia title/lead. Require zero. Run NER on `P'`: no old entity; `A'` recognized as type `T`. A second annotator answers `Q` from `P'` blind to the original; require `A'` as the answer. If the annotator cannot answer or gives another entity, drop.

**Two-rater validity protocol.** Sample 200 twins. Two independent raters, blind to the base item and model output, score a 7-item binary checklist:

1. `A'` is type `T`.
2. Old entity `A` and all aliases are absent.
3. `A'` is the unique answer to `Q` in `P'`.
4. `P'` is grammatical and coherent (5-point scale; pass ≥4 from both raters).
5. `Q` is unchanged in meaning and answer type.
6. No residual identifying cue to `A`.
7. The reasoning shape is preserved (same question, same relation, same answer type, passage length within ±10%).

Pass = both raters pass all seven; disagreements adjudicated by a third rater. Report percent agreement and Cohen’s κ; require κ ≥ 0.80. Require observed validity ≥90% with Wilson 95% lower bound ≥85%. At n=200, 92% observed gives a Wilson lower bound of 87.4%; 90% gives 85.1%; 95% gives 91.0%. So 200 audited twins is the minimum for a credible ≥90% claim. Do not use model compliance as the validity gate.

**Separation of validity and compliance.** Construction validity `V` is audited without the model. Model compliance `K = P(A' | twin passage)` and override `O = P(A | twin passage)` are outcomes with CIs. A conflict study’s power calculation is about `K` and `O`, not `V`.

**Feasibility.** A rough census of the 200 pilot items: 39 two-word capitalized answers (person/org-like), 26 date-like, 25 numeric, 19 single capitalized (GPE/other), 91 “other.” Only the first four categories are plausible NER twins; after the uniqueness/inventory/rewrite requirements, I estimate 15–25% of SQuAD items are eligible. At 20%, 200 twins require ~1,000 base items; 800 twins require ~4,000. This is a separate data-construction project, not a bolt-on.

### 4.3 Decision: drop the conflict claim from R1

**Recommendation: drop.** Reasons:

1. The pilot’s 74% is not a construction-validity measure; the v2 twins are type-incoherent. The original kill was based on a mislabeled metric, but the construction is still invalid, so the claim cannot be rescued from this pilot.
2. The closest conflict paper already reports a **92% valid conflict-generation rate** across its perturbation subgroups, including Entity Swap (Chen et al. 2026; manual audit, 50 per subgroup, no inter-annotator agreement). A new NER type-matched twin would need to beat 92% on a two-rater audit and show a clear delta. The marginal contribution would be methodological (better validity reporting), not phenomenal.
3. The twin rows were gold-only; `M` was never identified. Fixing that requires a new base/twin × passage/empty design, i.e., a new study.
4. The scoped paper is stronger without the conflict claim: it is a clean measurement paper with a valid estimand, not a paper with a fragile secondary claim.

If the team still wants the conflict study, it should be a **separate preregistered paper** with: NER type matching, document-level rewrite, 200 audited twins, two raters and κ, construction validity ≥90% (Wilson lower ≥85%), open-weight models, and a direct comparison to Chen et al. 2026’s Entity Swap. Do not spend the R1 N=800 budget on it.

---

## 5. What N=800 buys (MDE)

### 5.1 Assumptions

- Paired within-query design; the same 800 questions receive all arms.
- Pilot paired SDs: `SD_τ = 0.5576`, `SD_δ = 0.2641`. These are large because the outcomes are binary and the treatment effect is large; they imply ~42% discordant pairs for `τ`.
- Two-sided α = 0.05; power 80% and 90%; normal approximation.
- `MDE = (z_{1−α/2} + z_{power}) · SD / √n`.
- N=800 unique questions **per model**; 3 models; BM25 primary + dense-retriever replication. If N=800 is split across models or datasets, the per-cell MDE is larger (see §5.3).

### 5.2 Main table

| N (per model) | `τ` MDE₈₀ | `τ` MDE₉₀ | `δ` MDE₈₀ | `τ` 95% CI half-width |
|---:|---:|---:|---:|---:|
| 200 (pilot) | 11.05pp | 12.78pp | 5.23pp | ±7.73pp |
| 400 | 7.81pp | 9.04pp | 3.70pp | ±5.46pp |
| **800** | **5.52pp** | **6.39pp** | **2.62pp** | **±3.86pp** |
| 1,600 | 3.91pp | 4.52pp | 1.85pp | ±2.73pp |

The pilot effects are 32.5pp (`τ`), −7.5pp (`δ`), and 77.8pp (interaction). At N=800, the primary effect is ~5.9× the MDE; the delta is ~2.9×; the interaction is ~10×. The study is not underpowered for the effects the pilot observed.

### 5.3 Design allocations and interaction

| Design | n per cell | `τ` MDE₈₀ | Comment |
|---|---:|---:|---|
| 800 per model, 3 models | 800 | 5.52pp | Recommended |
| 800 total, split across 3 models | 267 | 9.56pp | Underpowered for 7B shrinkage; avoid |
| 800 total, split across 2 datasets | 400 | 7.81pp | Acceptable for a replication, not the primary |
| 800 total, split across 2 retrievers | 400 | 7.81pp | Acceptable if the retriever contrast is secondary |
| 800 total, per question-type stratum | ~114 | 14.63pp | Too wide for heterogeneity claims; report descriptively |

**Answer-presence interaction.** Using the pilot subgroup SDs (present 0.469, n≈436; absent 0.300, n≈364 at 54.5% recall), the interaction SE at N=800 is 0.0274, so **MDE₈₀ = 7.68pp**. The pilot interaction is 77.8pp; even a much smaller interaction is detectable. This is the key mechanistic test. If the 7B models have higher empty accuracy, both subgroup effects and the interaction may shrink; the MDE is still adequate for an 8pp interaction.

**Retrieval-dependent success share.** Bootstrap resampling the pilot to N=800 gives an approximate 95% CI half-width of **4.9pp** for `AF` (point ~81%, interval ~[76%, 86%]). At N=200 the half-width is ~9.6pp. This is a useful precision gain for the headline “four-fifths of top-1 successes are retrieval-dependent.”

**Model differences.** Two independent model estimates, each n=800, have a difference MDE of **7.8pp** for `τ`. With 3 models, the paper can say “positive in all three” and report the largest pairwise difference with a CI; it cannot claim a model-scale law.

**Sensitivity.** If the 7B models’ paired SD is larger than the pilot’s:

| `SD_τ` | N=800 MDE₈₀ | N=800 MDE₉₀ |
|---:|---:|---:|
| 0.45 | 4.46pp | 5.16pp |
| 0.50 | 4.95pp | 5.73pp |
| 0.5576 (pilot) | 5.52pp | 6.39pp |
| 0.65 | 6.44pp | 7.45pp |
| 0.70 | 6.93pp | 8.02pp |

Even at SD 0.70, N=800 detects a 7pp effect at 80% power. The primary effect would have to shrink below ~7pp for the study to be marginal. That is the real risk, not the arithmetic.

### 5.4 What N=800 buys — and what it does not

**Buys:**

- A 95% CI half-width of ±3.9pp for `τ` (vs ±7.7pp at N=200), enough to distinguish a large effect from a modest one.
- MDE 2.6pp for the random-passage penalty, so a null or a small negative effect is informative.
- MDE 7.7pp for the answer-presence interaction, the paper’s key mechanistic claim.
- Per-model replication: 3 models × 800, not one model with 2,400 queries. This is the right kind of generalization.
- A ±4.9pp CI for `AF`, making the “four-fifths” headline precise.
- A credible test of 7B shrinkage: if the effect falls below 5pp, the paper stops rather than spinning a null.

**Does not buy:**

- Model-family generality beyond the three chosen models; 3 is a replication, not a random sample of models.
- Dataset generality; SQuAD is one benchmark. Add a 200-item TriviaQA replication if budget allows, but do not claim general RAG behavior.
- Conflict/twin validity; dropped.
- Contamination bounds; dropped.
- Per-stratum heterogeneity; 14.6pp MDE per stratum is too wide. Report a forest plot descriptively, no “stratum X is larger” claims.
- Prompt robustness; that requires the 100-item symmetric-prompt check, not more N.

### 5.5 Recommended sample and budget

- **Primary:** 800 new SQuAD validation questions, disjoint from the pilot’s 200; stratified by question word with a minimum per stratum; 3 open-weight models (e.g., Qwen2.5-7B-Instruct, Llama-3.1-8B-Instruct, Mistral-7B-Instruct-v0.3); BM25 (Pyserini/Lucene) + one dense retriever (Contriever/E5); corpus = the 20,958 unique SQuAD train+val passages (or a standard Wikipedia corpus for the TriviaQA replication); arms E, S, T, O; randomized order; greedy decoding; max 32 new tokens. Answer-presence uses any gold alias after official SQuAD normalization, with a 100-item manual audit.
- **Calls:** 800 × 3 models × (4 arms BM25 + 2 arms dense T/E) = 14,400 generations. At ~50 tokens each, ~720k output tokens; on a 24GB GPU with vLLM, roughly 1–3 hours; cost **<$30**, well within the $50 budget.
- **Optional replication:** 200 TriviaQA questions × 3 models × 4 arms = 2,400 calls; <$10. This buys a second dataset, not more power.
- **Pre-run gate:** 100-item symmetric-prompt check on one model. If `τ < 10pp` under the symmetric prompt, stop and redesign; do not spend the full N=800.

---

## 6. Paper outline

**Working title:** *Retrieval Lift Is Conditional on Answer Presence: A Paired Ablation on Real Wikipedia Passages.*

**Venue:** Findings of EMNLP/ACL, or TACL. Not a main-conference novelty paper; not NeurIPS/ICML. The contribution is measurement rigor, not a new method or theorem.

**Thesis (one sentence):** On real Wikipedia passages, the pilot estimate is that top-1 retrieval raises exact-match QA accuracy by 32.5pp; about four-fifths of top-1 successes are retrieval-dependent, but the entire lift is concentrated in queries whose passage contains the answer — when the answer is absent, retrieval is worse than no retrieval. The N=800 study re-estimates every quantity.

**Contributions:**

1. A within-query paired retrieval ablation with randomized order and pre-registered inference, on real passages rather than synthetic or selected distractors.
2. An exact decomposition of retrieval lift into answer-present and answer-absent components, with a pre-registered interaction test.
3. A retrieval-dependent success share (`AF`) that quantifies how many top-1 successes would not occur without retrieval, without claiming a contamination bound.
4. A multi-model, multi-retriever replication that quantifies how much of the lift survives 7B-scale parametric knowledge and a stronger retriever.

**Section plan:**

1. **Introduction.** RAG evaluations report end-to-end accuracy; retrieval lift conflates retrieval recall and passage reliance. State the four claims and the scope (single-hop extractive QA, public Wikipedia benchmarks, no conflict/contamination claims).
2. **Related work.** Five closest papers table (§3); explain the measurement delta, not a phenomenon claim. Explicitly say what R1 does not do (no twins, no contamination bound, no minimax theorem).
3. **Design.** Corpora, retrievers, models, arms (E/S/T/O), prompt template (symmetric), randomization and blocking, scoring, sample size and MDE, pre-registration.
4. **Estimands and analysis.** Potential outcomes; paired differences; Wald and query-cluster bootstrap CIs; decomposition; interaction; `AF`; order-effect check; missing-data handling; multiple-testing gatekeeping (C1 → C2 → C3/C4).
5. **Results.** Main paired estimates (Figure 2); answer-presence decomposition (Figure 3); `AF` by model; oracle gap; per-stratum forest plot (descriptive); per-retriever; prompt-symmetry and order-effect robustness.
6. **Threats to validity.** Top-1 is not gold; prompt asymmetry; SQuAD contamination/empty accuracy; single-hop extractive scope; exact-match metric; random-passage definition; no conflict/twin claim; no contamination bound.
7. **Conclusion.** Retrieval lift is real but conditional; report answer recall and conditional reliance alongside end-to-end accuracy; random non-answer passages are not a neutral baseline in this setup.
8. **Appendix.** Pre-registration, prompts, ledger schema, power calculations, full per-stratum/per-model tables, search log.

**Figures/tables:**

- Figure 1: design schematic (4 arms, randomized order, paired within query).
- Table 1: retrieval diagnostics (paragraph hit, answer recall, passage length) by retriever.
- Figure 2: `τ`, `δ`, oracle gap by model, with 95% CIs.
- Figure 3: `τ_present` vs `τ_absent` by model, with interaction CIs.
- Table 2: accuracy by arm/model; `AF` with bootstrap CIs.
- Figure 4: per-stratum forest plot (descriptive).
- Table 3: prompt-symmetry and order-effect robustness.
- Appendix: full ledger summary and pre-registration hash.

**Analysis plan / pre-registration:**

- Freeze the analysis script and estimands before the cloud run; publish a hash.
- Primary: `τ > 0` (two-sided 95% CI; directional claim).
- Secondary confirmatory: interaction `τ_present − τ_absent > 0`; `δ < 0`; `AF` with bootstrap CI. Use a fixed-sequence gate: `τ` first, then interaction, then `δ`/`AF` with Holm.
- Exploratory: per-stratum, per-model, per-retriever, oracle gap, prompt variant, order effects.
- Sample: 800 new SQuAD validation IDs, seed, stratification, exclusion rules (no answer alias, passage truncation, generation error) frozen in advance.
- Scoring: official SQuAD normalization; EM primary, F1 secondary; 100-item human audit; inter-annotator agreement; error analysis.
- Missing data: 3 retries; if still error, report and sensitivity analysis treating errors as incorrect.
- Order effects: block randomization; test order coefficient; if >5pp, adjust/stratify.

**Stop conditions (pre-registered):**

1. `τ < 5pp` or 95% CI includes 0 at N=800 → stop; no paper.
2. Interaction `τ_present − τ_absent < 5pp` or CI includes 0 → drop the “conditional passage reliance” claim; report only `τ`/`δ` as a measurement note; likely stop.
3. `δ > 0` (random passages help or are neutral) → drop the “random-passage penalty” claim; report a null; the paper’s story weakens but `τ`/interaction can still stand.
4. Empty accuracy `C > 0.60` in any model → ceiling/contamination; switch to harder questions or stop; `τ` is uninformative.
5. Order effect >5pp or scoring audit error >5% → fix before analysis; do not proceed.
6. `τ < 0` in 2/3 models → stop; the effect is not robust.
7. Symmetric-prompt 100-item check `τ < 10pp` → do not spend the full N=800; redesign the prompt/task.

**Adjusted scores (honest):** Nov 6.0–6.25, Method 7.0–7.5, Math 5.0–5.25, Exp 8.0–8.5, Data 6.0, Journal 7.0, Feas 8.5, Compute 9.0. Core ≈ 6.5–6.9, Weighted ≈ 6.6–6.9. The scoped paper is a solid Findings paper, not a 7.4-core paper. The drop is the cost of removing invalid claims.

---

## 7. Verdict and next steps

**Verdict: CONDITIONAL GO for a bounded measurement paper; NO-GO for the conflict/twin/contamination/theory claims.**

The pilot’s primary effect is strong, precisely estimated, and survives independent ledger aggregation. The literature delta is real but measurement-level: I did not find a paper that randomizes within-query top-1/shuffled/empty arms with paired population inference and an answer-presence decomposition. The closest papers are descriptive (Maekawa, Amiraz, Joren), attribution methods (AttriBoT, ContextCite), or contamination detectors (Sela). The conflict space is occupied by Chen et al. 2026’s 92%-valid conflict perturbations (including Entity Swap) and Sun et al. 2026’s task-conditioned conflict study; the contamination claim is invalid because `C` conflates contamination with reasoning/guessing/priors.

**Sequence:**

1. **Fix the prompt asymmetry.** Write the symmetric prompt; run a 100-item pilot on one model (CPU/one GPU hour). If `τ < 10pp`, stop and redesign. Do not skip this; it is the biggest threat to the pilot effect.
2. **Pre-register.** Freeze estimands, sample, exclusions, scoring, gatekeeping, stop rules; publish a hash.
3. **Run N=800.** 800 new SQuAD questions × 3 models × 4 arms (BM25) + dense replication; ~14,400 calls; <$30; 1–3 hours on a 24GB GPU.
4. **Analyze.** Primary `τ`; interaction; `δ`; `AF`; oracle gap; per-model/per-stratum/per-retriever; order/prompt robustness.
5. **Write only if the gates pass.** If `τ < 5pp` or the interaction is absent, stop. Do not rescue with post-hoc subgroups.
6. **Do not run twins.** If the team wants the conflict paper, treat it as a separate preregistered study with the §4 protocol, 200 audited twins, two raters, κ, and a direct comparison to Chen et al. 2026. It is not part of R1.

**Budget:** ~$30 for the primary, <$10 for the optional TriviaQA replication, <1 day of engineering. The paper is cheap; the risk is novelty, not cost.

**If the portfolio needs an 8+ paper:** R1 is not it. Reallocate to T1 (if the theorem gate passes) or E2 (if the scale-up gate passes). R1 is a credible 6.5–6.9 Findings paper with a clean estimand; that is worth writing only if the team values a rigorous measurement paper over a higher-variance novelty bet.

---

## Appendix A — pilot recomputation details

- Ledger: `ledger_P_I08b.csv`, 700 rows. Base: `is_twin = 0`, 600 rows, 200 qids × 3 arms. Twins: 50 v1 (`qid 1000–1049`) + 50 v2 (`qid 2000–2049`). v1 excluded from the verdict.
- `τ`: paired `gold − empty`, mean 0.325, SD 0.5576, SE 0.0394, 95% CI [0.2477, 0.4023].
- `δ`: paired `shuffled − empty`, mean −0.075, SD 0.2641, SE 0.0187, 95% CI [−0.1116, −0.0384].
- Accuracies: top-1 0.400; shuffled 0.000; empty 0.075.
- Answer recall 109/200; paragraph hit 104/200; `P(EM | present) = 80/109 = 0.7339`; `P(EM | absent) = 0/91`.
- `τ_present = 0.679` (SE 0.045); `τ_absent = −0.099` (SE 0.031).
- `AF = 0.325 / 0.400 = 0.8125`; bootstrap 95% CI [0.704, 0.901] at N=200.
- Order effects (descriptive): top-1 0.369/0.397/0.433 for order 0/1/2; shuffled 0/0/0; empty 0.053/0.103/0.076. No monotone order trend, but the sample is small; the N=800 design will test it formally.
- v2 twins: 26/50 strict EM, 37/50 contains-new, 50 built. The 74% is model compliance; the construction is not NER-type-matched.

## Appendix B — formulas

- `MDE = (z_{1−α/2} + z_{power}) · SD / √n`, two-sided α, normal approximation.
- `τ = π · τ_present + (1 − π) · τ_absent`, exact by iterated expectation.
- `AF = τ / E[Y(T)]`, ratio estimator; bootstrap by query.
- Interaction SE: `√(SD_present²/n_present + SD_absent²/n_absent)`; independent subgroups.
- Wilson interval for twin validity; require lower bound ≥0.85 at n=200.

## Appendix C — search/verification log

- Queries: 5 closest paper titles/IDs; descriptive retrieval ablation; RAG attribution; contamination detection; knowledge conflict; counterfactual retrieval; passage reliance.
- Sources: arXiv abstract pages, ACL Anthology, HTML full-text probes. No paid API.
- Verified abstracts: 2505.06914, 2402.13492, 2411.06037, 2411.15102, 2409.00729, 2603.03203, 2605.14473, 2606.20245, 2026.findings-acl.202, 2606.21553, 2609.30337.
- Full-text probes: 2402.13492 (“without retrieval” 1 hit; “confidence” no hit); 2505.06914 (“random” 3 hits; “empty” no hit); 2411.15102 (“confidence interval” no hit); 2605.14473 (“entity swap” 12 hits; 92% valid conflict generation in Appendix B).
- Limitation: this is a targeted recheck, not a systematic review. The “no direct preemption” statement is bounded by the searched sources and date. Camera-ready should repeat the search and cite exact sections.

---

*End of report.*
