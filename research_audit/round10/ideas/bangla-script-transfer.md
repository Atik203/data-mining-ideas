# Bangla Across Scripts: Native-to-Romanized Transfer and Contrastive Alignment

**Internal short name:** M1

**One-line:** A model trained on native Bangla script loses accuracy when the same text is written in romanized Bangla; does contrastive alignment close the gap, and how much is information loss?  
**Score:** Core **6.00** / Weighted **6.50** — **CONDITIONAL; empirical paper only, native audit first.**  
**Independence:** 8/10.

## What

M1 measures the **script gap**: the accuracy difference between native Bangla and romanized Bangla (e.g., “ami bhalo achi” instead of “আমি ভালো আছি”) for a model trained on native script. It then tests whether paired contrastive alignment closes part of the gap, using a shuffled-pair placebo to separate a real method effect from the extra loss term.

## Why

Romanized Bangla is common in social media, chat, and user-generated text, but most Bangla NLP data is in native script. A model that looks strong on a native benchmark may fail silently on romanized input. The original M1 proposal claimed a minimax bound with an information-loss term \(\eta\); that theory is dropped, but the measurement is still useful and logically prior to any bound.

## How

1. Build a 9,000-item paired corpus: every item exists in native and three romanizations.
2. Freeze one document-clustered, group-stratified split (4,500 train / 900 dev / 3,600 test).
3. Train Qwen2.5-7B with QLoRA in three arms: native+R1 baseline (A), paired InfoNCE alignment (B), and shuffled-pair placebo (P).
4. Evaluate the script gap, alignment gain, placebo contrast, and the loss-effect ladder R1/R2/R3.
5. Audit the romanization with native speakers before any GPU spend.

## Dataset

**M1-Bangla-Paired-9k:** NLI 3,000 (IndicXNLI-Bengali + fresh human-written items); sentiment 3,000 (public Bangla SA + 500 fresh); MCQ-QA 3,000 (Bangla QA converted to 4-way MCQ with matched distractors). Three romanizations: R1 current deterministic scheme, R2 lossless/reversible, R3 lossy social-media style. A 500-item human-written romanization set checks ecological validity.

## Method

Qwen2.5-7B-Instruct, 4-bit NF4, LoRA r16/α32, 3 fixed epochs, no early stopping. Primary estimands: script gap \(\Delta_{\text{script}}\), alignment gain \(\Delta_{\text{align}}\), placebo \(\Delta_{\text{placebo}}\), loss effect \(\beta_\eta\). Document-clustered bootstrap; seed-level paired t for alignment. Native audit: 900 items, two annotators, meaning/readability/ambiguity, back-transliteration, and human-written validation.

## Expected

A script gap of at least 3.5pp, an alignment gain of 3.6–4.8pp (MDE90), a placebo near zero, and a loss effect that is larger for the lossy R3 than the lossless R2. If the 7B gap is under 2pp with a CI upper bound below 2pp, the scale result is “scale closes the gap” and the method line stops.

## Results

Pilot (Qwen2.5-1.5B, 200 IndicXNLI-Bengali pairs, 4-bit LoRA):

| Quantity | Result |
|---|---:|
| Native accuracy | .518 |
| Romanized accuracy | .400 |
| Script gap | **11.82pp** (paired \(t=1.96, p=.052\)) |
| Alignment gain | **+6.36pp** (\(t=1.54, p=.127\)) |

The direction is positive, but the pilot’s 110 “cells” are **89 unique items in 7 source groups**; the two seeds test different groups (only 21/89 items overlap). Seed 42 gap=19.57pp; seed 1234 gap=6.25pp. Cluster-bootstrap 95% CI: **[0.35, 25.06]pp**; honest MDE80 ≈ **22–32pp**. The InfoNCE used batch 2, so each anchor’s only negative was itself — an attract-only loss, not contrastive. The 30/30 romanization check was mechanical conformance, not native-speaker validation.

**Gate F:** proceed as an empirical paper only. At 9k/7B, the script-gap MDE90 is **3.4–3.9pp**; alignment MDE90 is **3.6–4.8pp**. The native audit costs ≈ **$320**; total ≈$340–355 and 52–76 GPU-hours. The \(\eta\) theorem is dropped from the claim set.

## Outcome

**Run the native audit first (Gate G0).** Do not rent a GPU before it passes. If the audit fails (>2% label-changing ambiguity or <95% meaning ≥4/5), stop M1. If it passes, run the 9k/7B study with a frozen split and a shuffled-pair placebo. Without the audit, frame the corpus as “machine-audited” and do not claim native validation. This is a 6.0–6.5 paper, not the main lead.
