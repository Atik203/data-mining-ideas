# Prompt for Claude: Q1 Research Idea Search Under a $50 Budget

Copy everything below the line into Claude. It is self-contained.

---

You are a senior ML/NLP researcher and adversarial reviewer. Your job is to find a genuinely novel, mathematically defensible Q1 research idea executable by a 3–4 person CS team in 6–7 months under a **$50 total cash budget**. Do not inflate scores. A false 8+ is worse than an honest 7. Your output will be used to decide where to spend six months, so every claim must survive a closest-paper check and a theorem audit.

## 1. Hard constraints

- **Cash:** ≤ **$50 total**. No paid API, no human annotation, no cloud A100, no article processing charge (APC).
- **Compute:** local **RTX 3060 6 GB**, free Colab/CPU. No large-model training.
- **Models:** GPT-2, Pythia-70M/160M/410M/1B, Qwen2.5-0.5B/1.5B, Llama-3.2-1B; small tabular/GNN models.
- **Data:** public or synthetic only. No licensed, restricted, or IRB data. No paid human studies.
- **Team:** 3–4 people; **6–7 months**.
- **Target:** Q1 journal (e.g., TACL, Computational Linguistics, JMLR, Machine Learning, Neural Networks, Biometrics, JRSS-B, IEEE TIFS, IEEE TKDE). Verify the current quartile and APC policy; do not assume.
- **No:** LLM-agent benchmarks that need paid API volume; large-model interpretability; human-subject studies; “framework/protocol” papers.

## 2. What counts as a contribution

A contribution must change at least one of these:

- a **new estimand** (a target that was not previously defined or identified);
- a **new identification result** (a proof that the target is learnable from observable data);
- a **new theorem, minimax bound, or lower bound**;
- a **new algorithm with a guarantee** that existing baselines cannot match;
- a **robust empirical law** that predicts behavior across models/tasks and is not a benchmark score.

It is **not** enough to:

- apply a known method to a new dataset;
- combine two known methods;
- propose a new benchmark with no new scientific question;
- rename an existing estimand;
- report a new application of an LLM.

**A new application is not a new theorem.** If the closest paper already has the same estimand and a known identification argument, the idea is at most a 6.

## 3. Search space

Cover **at least 8 genuinely different problem classes**. Suggested classes (choose the strongest, not all):

1. NLP semantics, compositionality, linguistic generalization.
2. LLM reasoning, planning, chain-of-thought, in-context learning.
3. Learning theory of small transformers.
4. Optimization, generalization, memorization, scaling.
5. Causal inference with text or tabular data.
6. Causal representation learning / identifiability.
7. Multi-agent learning, RL, game theory.
8. Human–AI decision theory, deferral, calibration.
9. Privacy, fairness, robustness, unlearning.
10. Graph, time-series, continual learning.
11. Experiment design, active learning, decision theory.
12. Scientific ML / symbolic discovery.

### Exhausted areas from a previous search — avoid unless you have a genuinely new theorem

These were checked and killed. Do not re-propose them without a new mathematical result:

- **Tokenizer comparison / merge-level Shapley / witness coverage.** Closest: Lesci et al. 2025 (ACL, causal subword inclusion); TokSuite (2512.20757); MorphScore (2507.06378); Coverage Principle (2505.20278); Sälevä & Lignos 2023.
- **CoT faithfulness / trace identifiability / token-level mediation.** Closest: Paul et al. 2024 (2402.13950); Fu et al. 2026 (2610.31733); Wei et al. 2026 (2606.21821); Meek et al. 2025 (2510.27378); Anwar et al. 2026 (2602.18297); Mohammadkhani et al. 2026 (2609.37312); Miles et al. 2017 (NIE partial identification).
- **Unlearning audit / influence-based deletion bounds.** Closest: Sarmasarkar et al. 2026 (2607.05898); Van Waerebeke et al. 2025 (2502.17323); Koloskova et al. 2025 (2506.06985).
- **Network sensitivity / spillover bounds under confounding.** Closest: Dorn, Guo & Kallus 2021/2025 (2112.11449); Ortyashov & Ghassami 2025 (2511.21534); Smith, Mathur & VanderWeele 2021 (Epidemiology 32(5):625–634).
- **LLM-agent evaluation, reproducibility prediction, experience reuse, AI code review, security advisories, supply-chain isolation.**

## 4. Per-idea protocol

For each candidate idea, produce:

1. **Precise question.** A formulation-level question, not an application.
2. **Target estimand/object.** Define it mathematically. State whether it is point-identified, partially identified, or only measurable.
3. **Closest 5 papers (2024–2026).** Exact arXiv IDs/DOIs. Open the abstracts/full text where possible. Label `verified`, `abstract-only`, or `unverified`. Do not rely on memory.
4. **Precise delta.** What is new: the estimand, the assumption set, the identification argument, the rate, or the algorithm. If the closest paper already has it, kill the idea.
5. **Theorem/model.** Definitions, assumptions, quantifiers, proof strategy, and a counterexample/failure condition. Check:
   - Does the new model **contain** the old model as a special case? If yes, the “sharp bound” cannot be a strict tightening.
   - Is the estimand **identified/measurable**? Is it distribution-dependent? Is the computation label observable?
   - Is the theorem a **standard result** applied to a new object? If yes, math ≤5.
   - Is the experiment **powered**? Give the minimum detectable effect, seeds, design effect, and an accuracy gate.
   - Is the dataset a **real artifact** or just a generator? Are the labels public?
6. **Experiment plan.** 6–7 months, local/Colab; state GPU-hours, CPU-hours, storage, and cash (must be ≤$50).
7. **Scores (0–10).** Use the rubric in §5.
8. **2–3 day desk check.** One cheap, decisive test with explicit kill criteria. The check must be CPU-only or <20 GPU-hours.

## 5. Scoring rubric

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
| 10 | **Independence** | 5% | Not a superficial extension of the team’s prior projects |

**Core** = mean of parameters 1–6. **Weighted** = sum of score × weight.

**Discipline:**

- A claim of **8+** requires novelty ≥8, method ≥8, math ≥8, and a verified theorem.
- If the theorem is standard, **math ≤5**.
- If the closest paper has the same estimand, **novelty ≤5**.
- If the experiment is underpowered or the dataset is synthetic-only, **experiments/dataset ≤6**.
- Report both the **scan score** and the **audit-adjusted score**. Scan scores are typically optimistic by **1.5–2.8 points**; the audit-adjusted score is the one that matters.
- At most **two** ideas may be flagged 8+. If none, say so explicitly.

## 6. Mandatory adversarial audits (before recommending)

Apply all of these to the top 2–3:

1. **Closest-paper check.** Search exact phrases and the identification literature, not just the topic. Open the closest theorem paper, not only the abstract.
2. **Theorem audit.** Prove or disprove; find a counterexample; check quantifiers, determinism, monitor access, support, and containment.
3. **Estimand audit.** Define the pair distribution, equivalence relation, computation label, and monitor class; check circularity and capacity confounds.
4. **Prior-art audit.** Does the exact theorem already exist? If yes, what is the delta?
5. **Experiment audit.** Power, seeds, accuracy gate, compute, confounds.
6. **Dataset audit.** Public labels? reusable? or a generator?
7. **Collinearity audit (measurement papers).** If the new metric is >0.8 correlated with the old metric, it is not a new construct.
8. **Leakage/checksum audit.** If the trace/feature leaks the answer, the separation is trivial.

## 7. Failure modes to avoid

These killed every 8+ claim in a previous search:

- **Known result in new clothing.** A Shapley identity, a standard partial-identification LP, a two-sample bound, or a communication-complexity bound applied to a new object is not a new theorem.
- **Theorem false as stated.** Check the quantifiers and the mapping between the model and the mathematical object. A single counterexample is enough to kill.
- **Model containment.** If the new model contains the old model as a special case, the new set cannot be a strict tightening.
- **Estimand undefined.** A “gap” that depends on an unspecified pair distribution, equivalence relation, or monitor class is not a target.
- **Circularity.** If the computation label is “whatever the model does,” the trace is faithful by construction and the estimand is not testable.
- **Underpowered experiments.** A 3-seed ICC gate at the true threshold can pass only ~48% of the time. Compute the MDE before promising a result.
- **Synthetic-only datasets.** A new generator is not a dataset contribution unless it enables a claim no natural benchmark can test.
- **Collinearity.** A new metric that is 0.88 Spearman with the old metric is not a new construct.
- **Checksum leakage.** A checksum of the answer identifies the answer; it is not a counterexample to faithfulness.
- **Crowded applications.** “Another benchmark” or “another LLM application” is not a Q1 method.

## 8. Output format

Write **one self-contained markdown report** with:

1. **Executive verdict** and a shortlist of at most 3 ideas.
2. **Search coverage table** (classes, ideas, scores, verdict).
3. **For each shortlisted idea:**
   - one-page brief (question, estimand, theorem, prediction);
   - full 10-parameter score table;
   - closest papers with exact IDs and verification status;
   - theorem, assumptions, proof strategy, counterexample/failure;
   - dataset and experiment plan;
   - budget and timeline;
   - risks and kill criteria.
4. **Rejected ideas table** with the fatal issue.
5. **A 2–3 day audit plan** for the top 2.
6. **Honest conclusion:** either a defensible 8+ or the best 6.5–7.5 paper.

Also write a **CSV ledger** with columns:
`idea,title,authors_or_source,year,venue_status,identifier,url,problem,estimand,method,guarantee,verified,notes`.

## 9. Final response

In the chat, return at most 450 words:

- top 2 ideas and their scores;
- budget for each;
- the single report path;
- one sentence on whether any idea is a genuine 8+, or whether the best is a 6.5–7.5 paper.

Be adversarial. Do not inflate. If no 8+ survives, say so plainly.
