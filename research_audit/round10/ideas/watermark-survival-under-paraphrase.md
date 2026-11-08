# Watermark Survival Under Paraphrase: Surface Change, Not Semantic Distance

**Internal short name:** T1

**One-line:** Can a text watermark survive paraphrasing, and can detection be predicted from surface changes rather than semantic distance?  
**Score:** Core **5.63** / Weighted **6.18** — **STOP as a theory paper; keep only a bounded measurement note.**  
**Independence:** 10/10.

## What

A watermark plants a statistical signal in generated text so a detector can later say “this came from model X.” A paraphrase attack rewrites the text while preserving meaning, hoping to erase the signal. The original T1 claim was a sharp phase transition: there is a critical semantic radius \(\rho_c(\delta,n)\); below it the watermark survives, above it detection collapses.

## Why

Watermarks are used for provenance, audit, and synthetic-content labeling. If a cheap paraphrase can erase them, the guarantee is weaker than advertised. A precise theory would tell a provider how much semantic distortion an attacker needs before detection fails.

## How

1. Generate paired clean and watermarked text with a white-box green-list scheme at 0.5B scale.
2. Paraphrase the watermarked text at weak and strong strength.
3. Run the detector on clean, watermarked, weak, strong, and a synonym-control arm.
4. Measure green-token retention, detector AUC, and MiniLM semantic distance.
5. Try to derive a critical radius from a semantic-distortion budget.

## Dataset

100 paired prompts; Qwen2.5-0.5B generator; a small local paraphraser; MiniLM embeddings for distance. This is a mechanism pilot, not a benchmark.

## Method

White-box green-list watermark; paraphrase attack; detector error rates; McNemar tests; semantic-distance curve. The proposed theory used a mean-radius attack class and a \(\chi^2\) key-average argument.

## Expected

The original expectation was a sharp threshold: detection stays high below \(\rho_c\) and collapses above it, with a formula for \(\rho_c(\delta,n)\).

## Results

The pilot found a strong mechanism signal:

| Arm | Detector rate |
|---|---:|
| Clean | .06 |
| Watermarked | .92 |
| Weak paraphrase | .88 |
| Strong paraphrase | .07 |
| Synonym control | .83 |

At \(\delta=2\), the green-fraction gap was .144 and AUC was .9886. Strong-vs-watermarked error difference was .85 (McNemar \(b=86, c=1, p\approx1.1\times10^{-24}\)). Weak paraphrase stayed close in MiniLM distance (.009); strong moved farther (.187). The synonym control retained 83% detection, so it is a weak control.

**Gate A killed the theory.** A mixture attack — mix identity with a key-erasing rewrite — gives a linear floor \(E^*(\rho)\ge\min(1,\rho/\rho_0)\), not a sharp threshold. The missing lemma \(\eta=\eta(\rho)\) is false: semantic radius does not identify whether green tokens survive, because formatting and retokenization at \(\rho\approx0\) already flip token-pair hashes. The \(\chi^2\) key-average proof is invalid for the shared additive key, and the threshold formula is negative at the pilot’s \(n=200\).

What survives is a **fixed-kernel retained-signal boundary**: \(r=1-\bar p\), where \(\bar p\) is the token-pair change rate; burstiness gives \(n_{\text{eff}}=n/\tau^2\); the boundary is \(\eta_c\approx0.39\), fitting 4/5 pilot arms with \(\tau\approx1.3\). But **Wang et al. (NeurIPS 2026)** already proves the \(\Delta\rho\) retained-signal threshold, so the empirical law is pre-empted.

## Outcome

**Stop the old 7B phase-boundary experiment.** Do not claim a minimax phase transition or estimate \(\rho_c(\delta,n)\). A short measurement/negative-result note on non-identifiability and the surface-change mechanism is possible, but it is not the lead and not an 8+ paper. Reallocate the theory time to E2 or RANKCAL.
