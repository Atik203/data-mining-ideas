# Tool-IV — Does Encouraging Tool Use Causally Improve Accuracy?

**One-line:** Randomly nudge a model to call a tool, then use the nudge as an instrument to estimate the causal effect of tool use on answer correctness.  
**Score:** Core **6.63** / Weighted **6.98** — **GO, conditional after calibration.**  
**Independence:** 8.5/10.

## What

Tool-IV asks a causal question: if the same model, on the same question, is pushed to use a tool, does accuracy improve? The treatment is tool use \(D\) (the tool is called and its output is inserted). The instrument is a randomized encouragement \(Z\): a strong nudge versus a neutral prompt, plus a placebo nudge. The estimand is a local average treatment effect (LATE) for questions whose tool use responds to the nudge.

## Why

Observational comparisons of “tool use” and “no tool use” are confounded: models may call tools on easier questions, or only when they are already confident. A randomized encouragement design identifies the effect for compliers. This is the extensive margin of tool use, distinct from E2’s intensive margin of observation content.

## How

1. Calibrate 400 items to find a difficulty band with baseline accuracy 0.60–0.70 and a first stage \(\gamma\ge0.35\).
2. Randomize three arms: control C (neutral), placebo P (irrelevant nudge), treatment T (strong tool nudge).
3. Record \(D\) (tool called and output inserted) and \(Y\) (answer correct).
4. Estimate ITT, \(\gamma=P(D=1\mid Z=1)\), and the Wald LATE \(\tau=\text{ITT}/\gamma\).
5. Report Balke–Pearl sharp bounds, the one-sided assignment-effect bound, and a placebo exclusion-sensitivity curve.

## Dataset

Pilot: 150 items, near ceiling. Confirmatory: **1,500 hard items** — MATH-500 levels 3–5, GSM8K hard, MuSiQue/HotpotQA — with 4,500 calls total: C 1,800 / P 900 / T 1,800. Calibration uses 400 items and no main-run outcome.

## Method

Randomized encouragement; one-sided compliance by construction (C and P cannot call the tool); stratified ITT; Wald LATE; correct Balke–Pearl linear program; item-level bootstrap; placebo \(\delta_p\) sensitivity \(\text{LATE}(\delta)=(\text{ITT}-\delta)/\gamma\) and breakdown \(\delta^*\).

## Expected

A positive LATE around 0.10, with a first stage strong enough (\(\gamma\ge.50\)) to make the IV informative. The pilot’s LATE point estimate was +0.10, but its CI was wide and baseline accuracy was at ceiling. Harder tasks are needed for headroom.

## Results

Pilot (N=150):

| Quantity | Result |
|---|---:|
| Control accuracy | .960 |
| Treatment tool-use rate | .267 |
| Treatment accuracy | .987 |
| ITT | +.027 |
| First stage \(\gamma\) | .267 |
| Wald LATE | +.100, 95% CI [−.098, +.298] |
| Correct BP ATE bound | **[−.693, +.040]** |
| One-sided assignment-effect bound | **[0, +.150]** |

Two corrections matter. The old one-sided bound width of 1.15 was a **code bug**; the corrected width is 0.15 at the pilot’s ceiling. The Balke–Pearl output had been inverted; the correct standard BP ATE bound is [−.693, +.040]. The pilot’s conclusion “bounds are vacuous” conflated three objects (BP ATE, assignment-effect bound, LATE) and misread the ceiling.

**Gate E:** 4,500 calls give an MDE for LATE of **0.089 at \(\gamma=.50\)** (0.099 at .45), about 3.1× tighter than the pilot. Calibration must show \(\gamma\ge.35\) and \(p_0=.60\)–.70 before the main run. Stop if \(\gamma<.35\), placebo \(|\delta|>.06\), or the LATE CI includes 0 with upper bound <.10.

## Outcome

**Run Stage 0 calibration first.** If it passes, run the 4,500-call hard-task study. If the LATE CI excludes 0 with a lower bound ≥.05 and exclusion is robust, this is a causal tool-use paper with sharp bounds and a placebo. If not, pivot to an ITT/measurement paper or stop. Do not rent a GPU before calibration.
