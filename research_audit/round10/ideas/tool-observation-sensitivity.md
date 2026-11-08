# Tool-Observation Sensitivity in Language-Model Agents: A Fixed-Prefix Causal Study

**Internal short name:** E2

**One-line:** If a tool returns a wrong value, does the agent’s next decision change — and how long does it take to recover?  
**Score:** Core **7.38** / Weighted **7.55** — **GO, staged; current main-track lead.**  
**Independence:** 7/10.

## What

An LLM agent calls a tool, reads the observation, and chooses the next action. E2 freezes the conversation immediately after a real tool observation, changes only the **content** of that observation at four severity levels, and measures whether the next decision changes. It also follows the episode for four steps to estimate how quickly the agent recovers.

## Why

Agent benchmarks usually report end-to-end task success. That mixes tool-use ability, planning, and environment noise. They do not tell us whether an agent notices a wrong price, a wrong date, or a contradictory policy. If agents silently propagate bad tool values, a “successful” demo can hide a serious reliability failure. E2 measures that sensitivity causally, within the same prefix.

## How

1. Harvest real post-tool prefixes from public \(\tau\)-bench trajectories.
2. Render the observation in the same prose format at four content levels: faithful (s=0), non-critical change (s=1), critical wrong value (s=2), critical plus contradiction (s=3). Add a verbatim JSON anchor (V).
3. Ask each target model for the next decision under every arm, paired within prefix.
4. Code the decision as same / silent propagation / flagged / action switch.
5. Continue the episode for four steps under transient and persistent injection to estimate recovery.

## Dataset

\(\tau\)-bench airline and retail historical trajectories: 1,980 public trajectories, **13,605 candidate post-tool decision points**, and **6,554 eligible** value-used observations after excluding profile/PII tools. The primary sample is **800 prefixes** (400 airline, 400 retail), with 80 held in reserve. The supply is measured, not assumed: min 7, median 38 eligible prefixes per task–domain pair.

## Method

Paired within-prefix design; five arms; three open-weight 7–8B models (Qwen2.5-7B-Instruct, Mistral-7B-Instruct-v0.3, Llama-3.1-8B-Instruct); greedy decoding; conditional-logistic slope of content switch on severity, clustered by prefix; silent-propagation decomposition; discrete-time recovery model with a Weibull shape check.

## Expected

A positive graded slope: more severe content errors produce more decision changes, with silent propagation a meaningful share. The pilot implies \(b\approx0.8\) logit per level, about 4× the MDE. Recovery is expected within a few steps when the error is transient; persistent errors should be harder.

## Results

**Pilot (N=60, qwen3.5:4b, synthetic prefixes):**

| Quantity | Result |
|---|---:|
| Consult rate | 54/60 = 90.0% |
| Placebo jitter | 0/60 = 0% |
| Error switch vs reference | 5/60 = 8.3% |
| 95% CI | [1.3, 15.3] pp |
| McNemar | \(b=5, c=0; p=.0625\) |
| Human spot-check | 12/12 labels agree |

This is a screening signal, not a dose-response law. The pilot is small, uses one model and synthetic prefixes, codes actions coarsely, and has no recovery data.

**Gate C design (8 Nov):** GO, staged. N=800 gives an MDE of **0.20 logit/level per model** (OR 1.22), 80% power; pooled ≤0.15. The pilot-implied \(b\approx0.8\) gives >99% power if the direction survives real prefixes. With 160–240 s=3 switchers, the per-model recovery half-life CI is ±16–20%, pooled ±9–11%; model/severity ranking needs 32–50% differences and is explicitly out of scope.

**Cost:** 35–45 GPU-hours on a 24GB rental ($15–22), $2–3 storage, **$0 annotation** (team-run), **$23 base / $70 hard cap**. No paid API.

## Outcome

**Run it.** Sequence: G0 harvest/freeze (2 CPU days, $0) → G1 calibration (3 GPU-h, ~$2) → main run (35–45 GPU-h) → labels/analysis (~1 week). Stop if G0 finds <880 eligible prefixes, G1 parse <95%, placebo jitter >20%, or s=3 switch is <10% or >80%. The claim is a bounded content-sensitivity and recovery result for open-weight 7–8B models — **not** a universal law or a model ranking.
