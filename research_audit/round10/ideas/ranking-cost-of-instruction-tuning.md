# The Ranking Cost of Instruction Tuning: Quality Beyond Accuracy and Calibration

**Internal short name:** RANKCAL

**One-line:** Instruction tuning often raises accuracy but can lower AUROC — so the model answers more questions correctly yet ranks correct answers less reliably.  
**Score:** Core **6.75** / Weighted **7.18** — **GO, conditional.**  
**Independence:** 8.5/10.

## What

RANKCAL asks whether instruction tuning changes **ranking quality** separately from accuracy and calibration. A model can become more accurate and still become worse at assigning higher confidence to correct answers than to wrong ones. That matters for selective prediction, abstention, and any workflow that trusts the model’s ordering.

## Why

Calibration papers usually study expected calibration error (ECE). But ECE and AUROC are different: isotonic regression can fix ECE while leaving the ranking gap intact. If instruction tuning systematically taxes ranking, then “better” instruction-tuned models may be worse at knowing which answers to trust. The pilot found exactly that pattern, but at too small a sample to confirm it.

## How

1. Evaluate a base model and its instruction-tuned sibling on the same MMLU items.
2. Compute accuracy, AUROC, AURC, and ECE.
3. Test the paired AUROC difference with DeLong’s method, clustered by subject.
4. Pre-register four coarse MMLU groups (STEM, Humanities, Social Sciences, Other) and report group estimates descriptively.
5. Use a TOST equivalence test at ±0.03 so that both a real tax and a true null are publishable.

## Dataset

MMLU, **2,500 items** in the confirmatory run. Primary models: Qwen2.5-7B base and Instruct. Replication: Llama-3.1-8B base and Instruct. Secondary label replication: ARC-Challenge (1,000) and GSM8K (1,000). The pilot used 500 MMLU items and 57 subjects.

## Method

Paired DeLong test for \(\Delta\)AUROC; subject-cluster bootstrap as a sensitivity; TOST at ±0.03 item-level (±0.04 cluster-robust); ECE and isotonic ECE as secondary; four pre-registered groups; a guardrail that withdraws “systematic” if any group with n≥400 has a positive point estimate above +0.02.

## Expected

The pilot direction is a **negative** rank change: accuracy rises from .512 to .544 while AUROC falls from .7652 to .7259. The confirmatory question is whether that −.039 tax survives at 7B with enough power. A true null is also a publishable result: “no meaningful rank tax at 7B.”

## Results

Pilot (500 MMLU items):

| Quantity | Base | Instruct | Change |
|---|---:|---:|---:|
| Accuracy | .512 | .544 | +.032 |
| AUROC | .7652 | .7259 | **−.0393** |
| ECE | .1333 | .1818 | +.0485 |
| Isotonic ECE | .069 | .062 | closes 84% of gap |

DeLong 95% CI for \(\Delta\)AUROC: **[−.0821, +.0034]**; item MDE ≈ .061. The 57-subject bucket leg was untestable (largest subject had 14 items; threshold was 15). κ_Y=.615 rejects exact rank preservation, but the bound \((1-\kappa)/2=.192\) is far too loose to test a .03 effect, so κ is demoted to a diagnostic.

**Gate E:** N=2,500 gives an item-level MDE of **0.028 AUROC** and a cluster-robust MDE of **0.036**. TOST ±.03 has power .82 item-level and .50 cluster-robust; if cluster-robust is mandatory, use ±.04 (power .85). Superiority at −.039 has power .97 item-level and .85 cluster-robust. The binding risk is not sample size; it is whether the 7B operating point is above chance and below ceiling.

## Outcome

**Run the 2,500-item study.** Both outcomes are publishable: a confirmed tax (“Instruction tuning taxes ranking, not just calibration”) or equivalence (“No meaningful rank tax at 7B”). Stop if the 90% CI is inconclusive or the group guardrail fires. This is the cheapest credible fallback if E2 fails; do not run both at once.
