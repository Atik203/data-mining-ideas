# Round 10 — T1 Theorem Gate: Semantic Radius → Green-Status Contraction

**Date:** 8 November 2026  
**Scope:** CPU-only, read-only audit. No GPU, no model loads, no model/dataset downloads, no pilot re-runs.  
**Sources read:** `round10/00_BRIEF.md`; `round9/pilot_idea1_whitebox.md`; `round9/01_idea1_watermark.md`; `round8/05_deep_theory_85.md` T1; `round9/pilot_idea1_wb/{results_wb.json, wb_gen.py, wb_para.py, wb_analyze.py}`; raw `resp_wb*.jsonl` (word-level recomputation only). Literature recheck performed today (new material finding, §7).

## Verdict

**WEAKEN — the mean-semantic-radius phase-transition theorem is false as formulated. The 1-D mean-radius critical radius ρ_c(δ,n) is killed. A weaker theorem survives: retention is governed by the surface pair-change rate p̄ (provable lemma), the finite-n boundary is in the retained signal r (equivalently Δρ), and a 1-D ρ_c exists only under a uniform/high-probability distortion constraint or for a fixed paraphrase kernel.**

The original theorem fails for four independent reasons, in decreasing severity:

1. **Formulation-level (fatal):** a mean-radius attack class admits mixture attacks. Under the null-closure required for any watermark-only lower bound, the minimax sum-error has a linear floor E\*(ρ) ≥ ρ/ρ₀. No sharp 0→1 jump at a positive ρ_c can exist. (Proposition 1.)
2. **Assumption-level (fatal to the lemma):** semantic radius does not identify green-status retention. The missing lemma "η = η(ρ), increasing" is false: counterexamples at ρ≈0, non-monotonicity, and a worst-case degeneration. (Proposition 2.)
3. **Proof-strategy level (serious):** the Round-8 χ² key-average cancellation is invalid for the implemented shared additive key (the key enters every hash as a common shift, so the key-average does not factorize), and the detector knows the key, so the key mixture is the wrong object. The lower bound must come from key erasure + null closure, not from a key-averaged χ².
4. **Formula level (decisive, fixable):** the threshold η(ρ_c) ≈ Δ/2 − √(log n/n) is quantitatively wrong. At the pilot's n=200, Δ=0.144 it is **negative**, predicting no detection even with no paraphrase; the pilot detects 0.92. The correct boundary is η_c = 1 − (z_{1−α}+z_{1−β})τ√(γ(1−γ)/n)/Δ ≈ 0.39, which fits four of five pilot arms with τ≈1.3 (d1 is bracketed by τ=1–1.3).

The repaired object is a **fixed-kernel retained-signal boundary** with r = 1−p̄, not a minimax over a mean semantic ball. This is close to the retained-signal interface of Wang et al. (NeurIPS 2026), which preempts the empirical law and forces the paper to pivot to the non-identifiability + surface-mechanism contribution (§7).

---

## 1. The assumption, exactly

Round-8 defines
\[
E^*(\rho,n;\delta)=\inf_\phi\ \sup_{P_0\in\mathrm{Human},\,P_1\in\mathrm{Watermarked}(\delta),\,K\in\mathcal K_\rho}
\big[P_0(\phi=1)+(K_\sharp P_1)(\phi=0)\big],
\quad
\mathcal K_\rho=\{K:\ \mathbb E[d_{\mathrm{emb}}(Y,K(Y))]\le\rho\},
\]
and claims: paraphrase "re-randomizes each token's green status independently with prob. η(ρ) increasing in ρ"; the critical radius solves
\[
\eta(\rho_c)\approx \Delta(\delta)/2-\sqrt{\log n/n};
\]
achievability has error \(\exp(-c'n(\Delta-2\eta)^2)\); impossibility holds for \(\eta\ge\Delta/2+\omega(\sqrt{\log n/n})\).

Four hidden assumptions are load-bearing:

| # | Hidden assumption | Status |
|---|---|---|
| H1 | η is a function of ρ (identifiability) | **False** |
| H2 | per-token i.i.d. re-randomization (no burstiness) | **False** for real paraphrasers |
| H3 | key-averaged χ² gives a known-key lower bound for the implemented hash | **False** |
| H4 | mean-radius ball has a sharp threshold | **False** (mixture floor) |

---

## 2. Fatal defect: the mean-radius formulation has no sharp threshold

### Proposition 1 (mixture floor)

Let the null class \(\mathcal P_0\) be closed under \(\mathcal K_\rho\) — i.e. for every \(K\in\mathcal K_\rho\) and every null \(P_0\), \(K_\sharp P_0\in\mathcal P_0\). (This closure is necessary for any *watermark-only* lower bound; without it a detector can detect the paraphrase itself, not the watermark.) Let \(K_{\mathrm{er}}\in\mathcal K_\rho\) be key-erasing with \(d_{\mathrm{emb}}=\rho_0\), and let \(K_{\mathrm{id}}\) be the identity. For \(\lambda\in[0,1]\) define the mixture
\[
K_\lambda=\lambda K_{\mathrm{er}}+(1-\lambda)K_{\mathrm{id}}.
\]
If \(\mathcal K_\rho\) is convex in the mean sense (as a mean-distance ball is), then \(K_\lambda\in\mathcal K_{\lambda\rho_0}\). The null member \(K_\lambda{}_\sharp P_0\) and the alternative \(K_\lambda{}_\sharp P_1\) agree on the \(\lambda\)-fraction (key erasure) and differ only on the \(1-\lambda\)-fraction. On the agreeing fraction the sum-error of **any** detector is exactly 1; on the differing fraction it is at least 0. Hence
\[
\boxed{\ E^*(\rho)\ \ge\ \min\{1,\ \rho/\rho_0\}\ } \qquad (\rho\le\rho_0).
\]

The bound is attained for this attack whenever the detector can separate the identity watermarked fraction from the erased fraction; in all cases it is a valid lower bound. Therefore the minimax error is a **linear ramp** from 0 to 1 over \([0,\rho_0]\), not a step at a positive \(\rho_c\ll\rho_0\).

### Corollary 1.1 (achievability is false at fixed positive radius)

For any fixed \(\rho>0\), \(E^*(\rho)\ge\rho/\rho_0>0\) for all \(n\). The claim \(E^*\to0\) for \(\rho\le c\rho_c\) is false whenever \(c\rho_c>0\), independent of \(n\), \(\delta\), or the watermark. Increasing \(n\) cannot remove the mixture floor.

### Corollary 1.2 (impossibility is false below the rewrite radius)

If \(C\rho_c<\rho_0\), the detector achieves sum-error \(C\rho_c/\rho_0<1\), so the claim \(E^*\ge1-o(1)\) at \(C\rho_c\) is false. The "no test beats chance" regime begins only at \(\rho_0\) (a full rewrite for every text), not at a small semantic radius.

### The null-closure dilemma

The theorem cannot escape by dropping closure:

- **If \(\mathcal P_0\) is not closed under \(\mathcal K_\rho\):** the alternative after a strong paraphrase is distributionally distinguishable from unparaphrased clean text. A paraphrase/style detector has low sum-error even when the green-count test is at chance. The pilot is in exactly this regime: only `wm_d2` was paraphrased; `clean` was not (`wb_para.py`, line 44–46). The strong arm's 0.07 green detection is therefore **not** an estimate of \(E^*\); it is an estimate of the green test's power. A paraphrase classifier can separate strong-paraphrased watermarked text from clean unparaphrased text.
- **If \(\mathcal P_0\) is closed under \(\mathcal K_\rho\):** Proposition 1 applies and the achievability side is false.

So the mean-radius formulation is internally inconsistent: the same closure needed for the lower bound destroys the upper bound. This is not a missing constant; it is a property of the functional.

### Repair

Use one of:
1. **Uniform distortion:** \(\sup_X d_{\mathrm{emb}}(X,K(X))\le\rho\). Then \(K_\lambda\notin\mathcal K_\rho\) for \(\rho<\rho_0\), and a genuine threshold can survive.
2. **High-probability distortion:** \(\Pr(d_{\mathrm{emb}}\le\rho)\ge1-\varepsilon\) with \(\varepsilon\to0\). A fixed \(\varepsilon\) leaves an error floor \(\varepsilon\); only \(\varepsilon\to0\) restores a sharp statement.
3. **Fixed kernel (empirical setting):** study one paraphrase channel \(K\), not a minimax over mixtures. Then \(E^*\) is replaced by the channel-conditional sum-error, which is what any real experiment can estimate. This is the only version the pilot data can support.

The pilot, and any fixed-paraphraser experiment, is case 3. The theorem's sup over \(\mathcal K_\rho\) is not identified by such an experiment.

---

## 3. The missing lemma is false: ρ does not identify η

### Proposition 2 (non-identifiability)

Fix an encoder \(d_{\mathrm{emb}}\) and \(\rho>0\). If the encoder has a non-trivial \(\rho\)-fiber — two distinct texts \(x\ne x'\) with \(d_{\mathrm{emb}}(x,x')\le\rho\) — then there exist two kernels \(K_0,K_1\in\mathcal K_\rho\) with \(\eta(K_0)=0\) and \(\eta(K_1)>0\). Hence no function \(\eta(\rho)\) is identified by the semantic ball alone.

**Construction.** \(K_0=\mathrm{id}\). \(K_1\) maps each text to a distinct text in its \(\rho\)-fiber (a bijection within the fiber, or a random draw). By definition \(K_1\in\mathcal K_\rho\). \(K_1\) changes the token sequence, so for a changed token pair the green status is a fresh hash draw; \(\eta(K_1)\) is the pair-change probability, which can be close to 1. The encoder's fiber structure is not constrained by the watermark.

**Why fibers are non-trivial.** A sentence encoder maps \(|\mathcal V|^n\) discrete texts into \(\mathbb R^d\) with \(d\approx384\). For any \(\rho\) above the encoder's quantization scale, near-collisions are unavoidable. Standard sentence encoders are also known to be insensitive to negation, word order, and some entity substitutions, and sensitive to synonym choice — the opposite of what a meaning metric needs. The semantic ball is therefore only as valid as the encoder; it is not a meaning-preservation certificate.

### Concrete counterexamples

**(a) Formatting/retokenization at ρ≈0.** Change `do not`→`don't`, remove punctuation, or alter whitespace. A sentence encoder maps these to nearly the same embedding (cosine distance ≪0.01), but the Qwen tokenizer's token IDs and the pair hash change, flipping green status at the edited positions. Hence \(\eta(0)>0\): the theorem's baseline \(\eta(0)=0\) is false. In the pilot, the control arm strips **all** punctuation and swaps 5% of words (`day2_run.py` `TOK` regex keeps only alphabetic words; `control_swap` joins with spaces). Word-level match is 94.2%, yet detection falls from 0.92 to 0.83. The drop is a surface/tokenization effect at a 0.052 embedding distance, not a semantic one.

**(b) Non-monotonicity.** \(K_A\): change one entity or number (1–2 tokens; potentially large embedding distance; \(\eta\approx2/n\)). \(K_B\): global synonym rewrite (many tokens; small embedding distance if synonyms; \(\eta\approx1\)). If \(d(K_A)>d(K_B)\) but \(\eta(K_A)\ll\eta(K_B)\), then η is not increasing in ρ. The pilot's weak-vs-control pair is a partial empirical instance: control has **5.6×** the MiniLM distance of weak (0.052 vs 0.009) but only **0.058** lower detection retention (0.895 vs 0.953), while its word-change rate is only 1.1× (0.058 vs 0.052). A smooth \(\eta(\rho)\) through the origin would predict a much larger control effect. This is screening-grade evidence (3 points), but it points the wrong way for ρ.

**(c) Worst-case degeneration.** If \(\mathcal K_\rho\) contains a key-erasing full rewrite (which it does for any \(\rho\) above the encoder's full-rewrite radius), then \(\sup_{K\in\mathcal K_\rho}\eta(K)=1\). The worst-case "η(ρ)" is a step at the rewrite radius, not a smooth curve; its location is an encoder/paraphraser constant, not a function of δ,n.

### Implication for the theorem

The missing lemma cannot be proved from \(\rho\) alone. The theorem must either (i) assume a surface-change budget directly, (ii) define the attack class by the retained signal rather than the semantic radius, or (iii) restrict to a fixed paraphraser family and estimate the bridge \(\rho\mapsto p̄\) empirically. Option (iii) is the only one the pilot can support.

---

## 4. The provable replacement lemma: pair-preservation retention

### Setup

Text \(X=(X_1,\dots,X_n)\), key \(k\), green list \(G_i(k,X_{<i})\) of size \(\gamma|\mathcal V|\). The detector statistic is
\[
G(Y,k)=\sum_{i=1}^n \mathbf 1[Y_i\in G_i(k,Y_{<i})].
\]
For the Kirchenbauer self-hash used in the pilot, \(G_i\) depends on \(Y_{i-1}\) only (window \(h=1\)). The watermarked source satisfies \(\mathbb E[G(X,k)]=n(\gamma+\Delta)\). The paraphrase is \(Y=K(X)\), key-oblivious, with pair-change indicators
\[
c_i=\mathbf 1[(Y_{i-1},Y_i)\ne(X_{i-1},X_i)],\qquad p_i=\mathbb E[c_i],\qquad \bar p=\frac1n\sum_i p_i.
\]

**Assumption (H) — hash mixing.** For any changed pair, the green indicator is a fresh Bernoulli(\(\gamma\)) draw independent of the original:
\[
\Pr(Y_i\in G_i(k,Y_{<i})\mid X,Y_{<i},c_i=1)=\gamma.
\]
This holds exactly if the key is random and the pair hash is 2-universal; for a fixed key it is an approximation over the text distribution, testable with a paraphrased-clean arm (§6).

### Lemma 1 (pair-preservation retention)

Under (H) and key-oblivious \(K\),
\[
\mathbb E[G(Y,k)]=\sum_i\Big[(1-p_i)\mathbb E[\mathbf 1[X_i\in G_i]]+\gamma p_i\Big]
= n\gamma+\sum_i(1-p_i)\Delta_i.
\]
For homogeneous \(\Delta_i=\Delta\),
\[
\boxed{\ r:=\frac{\mathbb E[G(Y,k)]-n\gamma}{n\Delta}=1-\bar p\ }.
\]
Equivalently, the green-status loss is
\[
\eta:=\frac{\mathbb E[G(X,k)]-\mathbb E[G(Y,k)]}{n\Delta}=\bar p.
\]

**Proof.** Condition on \(X\). If \(c_i=0\), the green status is unchanged. If \(c_i=1\), (H) gives \(\gamma\). Thus
\[
\mathbb E[\mathbf 1[Y_i\in G_i]\mid X]=(1-c_i)\mathbf 1[X_i\in G_i]+c_i\gamma.
\]
Take expectations; for a random key, key-obliviousness makes \(c_i\) independent of the original green indicator; for a fixed key this is the hash-mixing approximation checked by the paraphrased-clean arm. Sum. \(\square\)

### Corollaries

1. **Isolated edits.** A single changed token affects its own pair and the next pair, so \(\bar p\approx2p\) for a token-change rate \(p\). A window of \(h\) previous tokens gives \(\bar p\approx(h+1)p\).
2. **Burstiness.** The count variance is \(\mathrm{Var}(G)=n\gamma(1-\gamma)\tau^2\), with \(\tau^2=1+2\sum_{j\ge1}(1-j/n)\mathrm{Corr}(b_i,b_{i+j})\). The effective sample size is \(n_{\rm eff}=n/\tau^2\). Clause-level rewrites have \(\tau^2>1\); the i.i.d. model is a special case \(\tau^2=1\).
3. **Distribution-free w.r.t. semantics.** Lemma 1 needs no embedding, no meaning metric, and no model of the paraphraser. It needs only the pair-change rate and hash mixing. This is exactly the "distortion kernel compatibility" condition the theorem was missing.
4. **Elementary but load-bearing.** The lemma is simple; its value is that it is exact, testable, and replaces an unprovable assumption. It also explains why the measured retention should be paraphraser-specific and why semantic distance is a poor proxy.

---

## 5. Corrected theorem and threshold

### 5.1 Finite-n detectability boundary

Null: \(G_0\sim\mathrm{Bin}(n,\gamma)\) under (H) and a random key (or approximately for a fixed key with hash mixing). Test: reject if
\[
z=\frac{G/n-\gamma}{\sqrt{\gamma(1-\gamma)/n}}>z_{1-\alpha}.
\]
Alternative: mean \(n(\gamma+r\Delta)\), variance \(n\gamma(1-\gamma)\tau^2\). Power \(\ge1-\beta\) iff
\[
r\ \ge\ r_c(n,\delta,\alpha,\beta,\tau):=\frac{(z_{1-\alpha}+z_{1-\beta})\,\tau\sqrt{\gamma(1-\gamma)/n}}{\Delta}.
\]
Equivalently,
\[
\boxed{\ \eta_c=1-r_c=1-\frac{(z_{1-\alpha}+z_{1-\beta})\,\tau\sqrt{\gamma(1-\gamma)/n}}{\Delta}\ }.
\]

At \(\gamma=0.5\), \(n=200\), \(\Delta=0.1441\), \(\alpha=0.05\), \(\beta=0.2\), \(\tau=1\):
\[
r_c=0.610,\qquad \eta_c=0.390.
\]
With \(\tau=1.3\) (pilot estimate): \(r_c=0.793,\ \eta_c=0.207\).

The Round-8 formula \(\eta_c=\Delta/2-\sqrt{\log n/n}\) gives \(0.072-0.163=-0.091\) at the same cell: **negative**, i.e. it predicts failure even with no paraphrase. The pilot's unparaphrased watermarked arm detects at 0.92. The formula is falsified by existing data, not merely unproved.

### 5.2 Fit to the pilot

Predicted detection with \(\tau=1.3\) and the corrected boundary (power \(=\Phi(2r\Delta\sqrt n/\tau-z_{1-\alpha})\)):

| Arm | Δ | r | Predicted | Observed |
|---|---:|---:|---:|---:|
| wm_d1 | .0724 | 1.000 | .472 (τ=1: .656) | .630 |
| wm_d2 | .1441 | 1.000 | .932 | .920 |
| weak | .1441 | .953 | .910 | .880 |
| control | .1441 | .895 | .877 | .830 |
| strong | .1441 | .012 | .054 | .070 |

The corrected boundary fits d2/weak/control/strong within ~0.05 with τ≈1.3; d1 is bracketed by the τ=1 and τ=1.3 predictions. The original Δ/2 formula fits none of the detected arms. This is the strongest positive evidence in the gate: the *phenomenon* is real; the *parameterization* is wrong.

### 5.3 No sharp transition at finite n

If \(r_n=r_c+\theta/\sqrt n\), the power tends to \(\Phi(\theta/\sigma)\) smoothly; there is no 0→1 jump at finite n. A sharp jump requires \(r_n-r_c\gg1/\sqrt n\). The correct claim is a **detectability boundary with an \(O(n^{-1/2})\) crossover window**, not a phase transition. The "critical radius" is defined only up to that window, and only for a fixed kernel or a uniform-radius class (Proposition 1).

### 5.4 Corrected theorem (fixed kernel / uniform radius)

**Theorem 1 (achievability).** For a fixed key-oblivious \(K\) with retention \(r=1-\bar p>r_c\), the green-count test has sum-error \(\le\alpha+\beta\), with exponent at least \(n(r\Delta)^2/(8\gamma(1-\gamma)\tau^2)\).

**Theorem 2 (impossibility).** If \(K\) is key-erasing (\(K_\sharp P_{1,k}=K_\sharp P_0\) for all \(k\)) and the null class is \(K\)-closed, then \(E^*=1\) for every detector; if \(K\) is \(\varepsilon\)-key-erasing, \(E^*\ge1-\varepsilon\). No χ² is needed; this is the two-point bound \(E^*\ge1-\mathrm{TV}(P_0,Q)\).

**Theorem 3 (non-identifiability).** For any encoder with non-trivial \(\rho\)-fibers, \(\eta\) is not a function of \(\rho\); the 1-D \(\rho_c(\delta,n)\) is not identified. A 1-D threshold exists only along a specified rate-distortion frontier \(\bar p^*(\rho)\), or under a uniform/high-probability distortion constraint.

**Corollary (mixture floor).** For a mean-radius class and a \(K_\rho\)-closed null, \(E^*(\rho)\ge\min(1,\rho/\rho_0)\); hence no sharp threshold at \(\rho_c\ll\rho_0\).

---

## 6. Is a real paraphraser's distortion kernel compatible?

### 6.1 The pilot, recomputed at word level

CPU-only recomputation on the saved `resp_wb*.jsonl` (no retokenization; word-level match via `difflib.SequenceMatcher`):

| Arm | MiniLM d (mean±sd) | word match | implied p̄ = 1−match | detection | retention proxy* | 1−p̄ | change-run τ² |
|---|---:|---:|---:|---:|---:|---:|---:|
| weak | .0093±.0136 | .948 | .052 | .88 | .953 | .948 | 1.24 |
| control | .0522±.0308 | .942 | .058 | .83 | .895 | .942 | 0.49* |
| strong | .1873±.0699 | .202 | .798 | .07 | .012 | .202 | 1.68 |

\*Retention proxy = (det−det_clean)/(det_wm−det_clean), a monotone detection-rate proxy, not the mean green-fraction ratio; exact r requires retokenization, unavailable CPU-only. †The control's change flags are sparse and isolated; τ²<1 is a sparse-sequence artifact. For the boundary, τ² should be estimated from the post-paraphrase green indicators, not the change flags.

**What fits.**
- The detection-rate proxy for weak and control is consistent with \(r=1-\bar p\) within 0.05. The weak arm's 94.8% word match and 0.953 proxy are almost exactly the lemma.
- Strong is below the prediction (0.012 vs 0.202), as expected from burstiness (\(\tau^2=1.68\)) and the threshold: at \(r=0.20\), \(r<r_c=0.79\) (τ=1.3), so power falls below 0.2 (predicted 0.16, observed 0.07). The lemma predicts the *mean* retention; the detector's *power* is a nonlinear function of it.
- The corrected boundary with τ≈1.3 fits d2/weak/control/strong (§5.2); d1 is bracketed by τ=1–1.3.

**What does not fit the original model.**
- The per-token i.i.d. model (\(\tau^2=1\)) overpredicts power by 0.05–0.15 at d2/weak/control; the real kernel is bursty.
- A smooth \(\eta(\rho)\) is inconsistent with the weak-vs-control pair: 5.6× distance, 1.1× surface change, only 0.058 difference in retention. The surface rate explains the similarity; the semantic radius does not.
- Weak is not a paraphrase: 94.8% of source words are retained in sequence. The pilot has only **two** informative paraphrase points (control and strong), not four.

### 6.2 Data-quality and estimand caveats

1. **Null not closed.** Only `wm_d2` was paraphrased; `clean` was not. The strong arm's 0.07 green detection does not estimate \(E^*\); a paraphrase detector can separate strong-paraphrased watermarked text from clean unparaphrased text. The minimax lower bound requires paraphrasing **both** arms (as Wang et al. do).
2. **Truncation.** About 47–50% of weak and strong outputs lack terminal punctuation and were capped at 220 tokens; many paraphrases are cut mid-sentence. Length is not controlled.
3. **Control confound.** The control strips all punctuation and numbers and swaps 5.0%±2.2% of words; it is a formatting+lexical control, not a clean synonym-swap control. Its 0.052 distance is largely formatting.
4. **Meaning preservation unverified.** No human or NLI check. MiniLM distance 0.187 may be at or beyond the meaning-preserving boundary; the strong arm's content-word overlap is 26%.
5. **Single key, single model, n=100.** No key-robustness check; the 0.5B generator's watermark shift may differ at 7B.
6. **Key secrecy.** If the attacker can query the detector or knows the green lists, it can target red tokens and destroy detection at near-zero semantic cost (cf. arXiv:2411.05277, detection below 10%). The theorem's lower bound must state key secrecy; a key-aware attacker makes the semantic radius irrelevant. The pilot's paraphraser is key-oblivious, the favorable case.

**Compatibility verdict:** the real kernel is compatible with the **pair-preservation** model and with the corrected boundary, but **not** with the i.i.d. \(\eta(\rho)\) model. The pilot is screening-grade evidence for the phenomenon, not for the theorem.

---

## 7. Literature recheck: a material preemption

A targeted recheck today found a paper absent from the Round-8/9 closest-5 list:

**Wang, Huang, Cui, Zhong, Luo, "A Retained-Signal Interface for LLM Watermark Robustness under Paraphrase," NeurIPS 2026, poster #155532, paper #36189.**

What it proves/does (from the poster and slides, fetched today):
- Defines injected signal \(\Delta=\mathrm{KL}(P_1\|P_0)\) and channel contraction \(\rho=\mathrm{sdpi}(W)=e^{-2B}\); the product \(\Delta\rho\) governs post-paraphrase detectability.
- Proves Pinsker and Bretagnolle–Huber threshold laws, \(\Theta(\sqrt{\Delta\rho})\) sharpness for any \((\Delta,\rho)\)-only converse, and implementation-blindness at matched \(\Delta\).
- Gives a score-projected audit and validates on 108 cells (KGW/Unigram × Qwen/Llama × lengths × strengths): AUC MAE 0.016; \(\Delta\) alone Spearman 0.104 vs \(\Delta\rho\) 0.983.
- Finds "low detection without semantic collapse": 11/15 low-\(\Delta\rho\) cells keep BGE similarity ≥0.85.

**Impact.**
- The **empirical retained-signal law** and the "Δ alone is uninformative" claim are preempted. The original T1 headline ("no theory says when detection becomes impossible; we give the phase transition") is no longer novel at the functional level. The literal kill condition (a threshold proved under a semantic ball) is **not** triggered; the functional novelty is nevertheless preempted by the retained-signal threshold law.
- What is **not** preempted: (i) the non-identifiability of \(\rho\) from semantic radius (Proposition 2); (ii) the pair-preservation mechanism \(r=1-\bar p\) that explains *what determines* the measured \(\rho\); (iii) the mixture/null-closure impossibility for the mean-radius minimax (Proposition 1). These are the salvageable contributions.
- The paper must cite Wang et al. as the closest functional neighbor and reposition: "the retained-signal interface measures \(\rho\); we prove semantic radius cannot identify it and derive its surface mechanism." That is a mechanism/negative-result paper, not a new phase-transition paper.

Other boundaries (unchanged from the audit): Zhang et al. 2024 (impossibility with quality/perturbation oracles), Sadasivan et al. 2025 (TV bound for fixed attacks), WaterMax 2024 (operating curve), "Theoretically Grounded Framework" NeurIPS 2025 (distribution-level framework), arXiv:2411.05277 (key-aware paraphrase attack). None proves the semantic-ball \(E^*(\rho)\); Wang et al. is the closest and the most damaging.

---

## 8. Exact assumptions for the repaired theorem

| # | Assumption | Test / violation |
|---|---|---|
| A1 | **Key secrecy / key-oblivious attacker.** \(K\) is chosen without knowledge of \(k\). | If the attacker queries the detector, the bound is void (arXiv:2411.05277). |
| A2 | **Hash mixing / γ-balance.** For changed pairs, green status is fresh Bernoulli(γ); for the null, \(G\sim\mathrm{Bin}(n,\gamma)\). | Paraphrased-clean arm: mean green frac \(=0.5\pm0.01\); type-I \(\le0.10\). |
| A3 | **Fixed tokenizer; retokenization is part of \(K\).** | Report tokenizer; no cross-tokenizer comparison. |
| A4 | **Pair-change rate \(\bar p\) measured on the same tokenization as the detector.** | Compute with the detector tokenizer; report per text. |
| A5 | **Stationarity / finite \(\tau^2\).** Effective \(n_{\rm eff}=n/\tau^2\). | Estimate \(\tau^2\) from post-paraphrase green indicators. |
| A6 | **Key erasure + null closure for the lower bound.** \(K_\sharp P_{1,k}=K_\sharp P_0\); \(\mathcal P_0\) is \(K\)-closed. | Paraphrase both clean and watermarked; mixture audit (below). |
| A7 | **Uniform or high-probability distortion, not mean-only.** \(\sup_X d\le\rho\) or \(\Pr(d\le\rho)\ge1-\varepsilon\), \(\varepsilon\to0\). | Report the max and the 95th percentile of \(d\), not only the mean. |
| A8 | **Semantic validity of the encoder and paraphrase.** Human/NLI meaning preservation; encoder separates meaning-preserving from meaning-changing edits. | 100 texts/level, 2 annotators, κ≥0.6; encoder AUC≥0.8. |
| A9 | **No truncation; length controlled.** | Terminal punctuation; length ratio 0.8–1.2; regenerate if truncated. |
| A10 | **Pre-registered detector, α, and analysis.** | Freeze before the run; report all cells. |

---

## 9. Next experiment: T1-PAIR-7B

### 9.1 Objective

Test the **mechanism**, not the original phase transition: (i) does \(r=1-\bar p\) hold across paraphrase levels? (ii) does semantic radius \(\rho\) add incremental prediction beyond \(\bar p\)? (iii) does the corrected boundary \(r_c(\delta,n)\) order correctly? (iv) does the mixture floor \(E^*\approx\lambda\) appear under null closure? The original \(\Delta/2\) formula needs no experiment — the existing pilot already falsifies it at n=200.

### 9.2 Design

| Factor | Levels | N per cell |
|---|---|---|
| Watermark δ | 2, 4 | — |
| Length n (new tokens) | 200, 400 | — |
| Paraphrase level | L0 identity; L1 light (≤10% token change); L2 moderate (~30%); L3 strong (~60%); L4 full (~90%) | 400 base texts per (δ,n) |
| Anchor A1 | high \(\bar p\), low \(\rho\): global WordNet synonym substitution + clause reorder (CPU, no LLM) | 400 per n |
| Anchor A2 | low \(\bar p\), high \(\rho\): 1–3 entity/number/negation edits | 400 per n |
| Mixture audit | λ∈{0, .25, .5, .75, 1} of L4 mixed with identity, applied to clean **and** watermarked | 400 per λ at (δ=2,n=200) |
| Null-closed clean | all five levels, same paraphraser | 400 per n |

**Stage 1 (mechanism, ~8,000 texts, <$20):** anchors (2 arms × 400 watermarked + 400 clean per n, n=200) + mixture audit (λ∈{.25,.5,.75} × 400 × clean/wm, reusing L0/L4) + L0–L4 at δ=2,n=200 (400 wm + 400 clean per level). **Stage 2 (boundary, ~8,000 texts, <$30):** the remaining three (δ,n) cells × L0–L4, N=400, plus the n=400 clean arm. Grand total ~16,000 texts, ~25–35 GPU-hours on a 24 GB card, <$50 (within $50). Paraphraser: 7B at fixed temperature, max_new_tokens 1.2× source, no truncation; regenerate if cut. Detector: green-count z-test, γ=0.5, α=0.05, fixed pre-registered key; second key on a 100-text subsample for key sensitivity. Human validation: 100 texts/arm, 2 annotators, 5-point meaning score, κ≥0.6. Encoders: MiniLM (continuity) + BGE (robustness); report both, plus the 95th-percentile distance.

### 9.3 Estimands and analysis

1. **Retention curve:** per cell, \(r=(\bar G_{\rm post}-\gamma)/(\bar G_{\rm wm}-\gamma)\). Fit \(r=1-\beta\bar p\); test \(\beta=1\) by TOST with margin 0.15.
2. **Incremental ρ:** nested \(r\sim\bar p\) vs \(r\sim\bar p+\rho\) (and interaction); F-test and partial \(R^2\). Pre-register equivalence margin \(\Delta R^2=0.01\).
3. **Anchor 2×2:** high/low \(\bar p\) × high/low \(\rho\); test the main effects and interaction on \(r\) and detection. The causal claim is the anchor contrast, not the observational \(\rho\) regression.
4. **Boundary:** per cell, fit a logistic detection curve on \(\bar p\); report \(\bar p_{50}\) and its CI. Test the predicted ordering \(\bar p_{50}(\delta=4)>\bar p_{50}(\delta=2)\), \(\bar p_{50}(n=400)>\bar p_{50}(n=200)\).
5. **Mixture audit:** estimate \(E^*\) by type-I+type-II at the optimal threshold; test \(E^*=\lambda\) (slope 1) against the linear-floor prediction.
6. **Null-closure check:** clean-paraphrased green fraction \(=0.5\pm0.01\); type-I \(\le0.10\). If it fails, the hash is not mixing; stop.

Predicted boundaries (τ=1, \(r=1-\bar p\)): \(\bar p_{50}=0.39\) (δ2,n200), 0.57 (δ2,n400), 0.63 (δ4,n200), 0.74 (δ4,n400); with τ=1.3: 0.21, 0.44, 0.52, 0.66. The ordering is robust; the absolute values are the test.

### 9.4 Power / MDE

- **Detection difference, N=400/cell:** two-proportion MDE 0.099 at p=0.5, 0.085 at p=0.2–0.3 (α=.05 two-sided, 80% power). The expected weak→strong difference is ~0.8; trivially powered.
- **Retention per cell:** per-text green-fraction SD \(\sqrt{\gamma(1-\gamma)/n}\approx0.035\) at n=200; over N=400, SE of the mean 0.0018; \(r\) SE = 0.0018/Δ ≈0.012–0.018. The curve is estimated to ±0.04 or better.
- **Slope β:** SE≈0.02–0.04 across five levels; the TOST margin 0.15 is easily tested. A deviation of 0.1 from β=1 is ≥2.5 SE.
- **Incremental ρ:** N≈2,000 per (δ,n); 80% power at α=.05 detects partial \(R^2\approx0.004\). The equivalence margin 0.01 is testable with a 90% CI.
- **Anchor interaction:** 4 cells × 400; difference-of-differences SE≈0.05; MDE≈0.14 at 80% power. The predicted p̄ main effect is ≥0.4; the ρ main effect under the semantic hypothesis is ≥0.2. Adequately powered.
- **Boundary difference:** \(\bar p_{50}\) SE≈0.04; difference SE≈0.057; MDE≈0.16. The δ effect (0.24) is powered; the n effect (0.18) is marginal — raise Stage 2 to N=600/cell (adds ~20% cost) if the n comparison is primary.
- **Mixture slope:** five λ × 400; slope SE≈0.03; a deviation of 0.1 from 1.0 is 3 SE.

### 9.5 Stop conditions (pre-registered)

1. **Null-closure failure:** clean-paraphrased green fraction outside \(0.5\pm0.01\), or type-I >0.15. Fix the detector/hash; do not interpret \(E^*\).
2. **Mixture audit failure:** observed \(E^*<\lambda-0.15\) at any λ. The detector is exploiting attack identity (null not closed); the minimax estimand is invalid; stop.
3. **p̄ lemma rejected:** TOST fails, \(|\beta-1|>0.15\), or the high-\(\bar p\)/low-\(\rho\) anchor does **not** lower retention relative to the low-\(\bar p\)/high-\(\rho\) anchor by ≥0.15. Drop the p̄ theorem; report the empirical benchmark only.
4. **Semantic mechanism survives:** the high-\(\rho\)/low-\(\bar p\) anchor lowers retention by ≥0.15 relative to the low-\(\rho\)/high-\(\bar p\) anchor, and \(\rho\) adds \(\Delta R^2\ge0.01\). Then the semantic-radius story survives in a **uniform-radius** form; pivot to the encoder-validity paper.
5. **Boundary rejected:** \(\bar p_{50}\) ordering reversed, or outside the predicted value by >0.15 in ≥2 cells. The corrected threshold law is wrong; stop the theory claim.
6. **Phenomenon fails:** L4 (\(\bar p\ge0.8\)) detection >0.5 at δ=2,n=200. The pilot's collapse does not replicate; kill the robustness phenomenon.
7. **Manipulation invalid:** human meaning preservation <80% at L2/L3, or encoder separation AUC <0.8. The \(\rho\) axis is invalid; stop and redesign.
8. **Budget:** >$50 or >30 GPU-hours before Stage 1 completes. Cut Stage 2, not the anchors or the mixture audit.

### 9.6 What the experiment can and cannot show

- **Can:** whether the surface pair-change rate explains retention; whether semantic radius adds anything; whether the corrected \(r_c(\delta,n)\) orders correctly; whether the mixture floor appears under null closure.
- **Cannot:** estimate the original \(E^*(\rho)\) over a mean semantic ball (Proposition 1 says the object is ill-posed); prove a sharp phase transition at finite n; generalize beyond the tested paraphraser family and encoders. The paper must state these limits.

---

## 10. What dies, what survives, and the path

### Dies

- The **mean-semantic-radius phase transition** \(E^*(\rho)\) with a sharp \(\rho_c(\delta,n)\). Proposition 1: linear mixture floor; no sharp threshold; null-closure dilemma.
- The **i.i.d. per-token η(ρ)** model. Real paraphrases are bursty (\(\tau^2=1.68\) strong) and the weak arm is near-verbatim.
- The **Δ/2 threshold formula**. It is negative at n=200 and contradicted by the 0.92 detection; the correct \(\eta_c\approx0.39\).
- The **χ² key-average proof**. The shared additive key enters every hash as a common shift, so the key average does not factorize; the detector knows the key, so the mixture is the wrong object.
- The **novelty claim** that no 2024–26 paper proves a retained-signal threshold. Wang et al. NeurIPS 2026 does, with a 108-cell audit.

### Survives

- **Proposition 2 (non-identifiability):** semantic radius does not identify green-status retention. This is a genuine negative theorem, and it is not in Wang et al.
- **Lemma 1 (pair-preservation retention):** \(r=1-\bar p\), exact under key-random/hash-mixing, with \(\tau^2\) correction. It explains *what determines* the measured \(\rho\).
- **Theorem 1–2 (fixed-kernel/uniform-radius boundary):** the finite-n \(r_c\) formula, the \(\tau^2\) correction, and the key-erasure + null-closure lower bound.
- **The phenomenon:** the pilot's plant, weak-preserve, strong-destroy pattern is real and reproducible; the corrected boundary fits four of five arms with τ≈1.3 (d1 is bracketed by τ=1–1.3). The *explanation* changes, not the existence.
- **The experiment:** the 2×2 p̄×ρ anchors + mixture audit + δ,n boundary is a decisive, cheap mechanism test.

### Score impact (no inflation)

T1's theory Core should drop from **7.63** to **~7.1–7.2** unless repositioned as the non-identifiability/mechanism paper: Novelty 6.5–7.0 (Wang et al. preempts the empirical law; the negative theorem remains novel), Math 7.5 (the lemma and non-identifiability are rigorous but elementary; the χ² ambition is gone), Method 7.5 (the corrected boundary and the 2×2 design are sound), Exp 7.0 (pilot screening-grade; the new experiment is decisive but not yet run). The weighted score falls similarly. This is a downgrade, not a kill: the paper can survive as "semantic radius does not determine watermark retention; the surface-pair mechanism does," with Wang et al. as the retained-signal interface and the new experiment as the mechanism test.

### Recommendation

1. **Do not rent 7B for the original theorem.** The mean-radius object is ill-posed; more compute cannot fix it.
2. **Rewrite the claim set** to: non-identifiability + pair-preservation lemma + fixed-kernel finite-n boundary + mechanism experiment. Cite Wang et al. as the closest functional neighbor and the source of the retained-signal law.
3. **Run Stage 1 only** (anchors + mixture audit, ~8,000 texts, <$20) if the pivot is accepted. Stage 2 only if Stage 1 supports p̄ over ρ.
4. **Retire the phrases** "phase transition," "critical radius \(\rho_c(\delta,n)\)," and "minimax over semantic ball" unless the uniform-radius version is explicitly adopted.

---

## Appendix A — Pilot word-level recomputation (CPU, read-only)

Command: Python `re` + `difflib.SequenceMatcher` on `resp_wb.jsonl` (`wm_d2` text) vs `resp_wb_para.jsonl` (weak/strong/control), n=100.

| Arm | len ratio | Jaccard (all words) | positional match | sequence match | long-word Jaccard | change runs (mean) | max run | τ² |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| weak | 0.992 | 0.921 | 0.598 | **0.948** | 0.956 | 4.9 | 1.9 | 1.24 |
| strong | 1.053 | 0.205 | 0.015 | **0.202** | 0.260 | 19.6 | 37.4 | 1.68 |
| control | 0.995 | 0.884 | 0.814 | **0.942** | 0.967 | 8.1 | 1.3 | 0.49* |

Control swap fraction: 0.0505±0.0217 (min 0.006, max 0.102). Terminal punctuation: weak 0.53, strong 0.50, control 0.00 (punctuation stripped by construction). The control is a formatting+5% lexical edit, not a clean synonym control.

## Appendix B — Key calculations

- Corrected \(r_c\) (γ=.5, α=.05 one-sided, β=.2, τ=1): \(n=200\): \(2.487\sqrt{.25/200}/.1441=0.610\); \(n=400\): 0.431. With τ=1.3: 0.793, 0.560.
- \(\eta_c=1-r_c\): 0.390 (n=200), 0.569 (n=400); τ=1.3: 0.207, 0.440.
- Original formula at n=200: \(.0721-\sqrt{\log 200/200}=.0721-.1629=-.0908\).
- Power at d2 (r=1, τ=1.3): \(\Phi(2\cdot.1441\sqrt{200}/1.3-1.645)=\Phi(1.490)=.932\).
- Two-proportion MDE, N=400: \(2.802\sqrt{2p(1-p)/400}\): .099 at p=.5; .085 at p=.2–.3.
- \(r\) SE per cell: per-text green-fraction SD \(\sqrt{.25/200}=.0354\); over N=400, .00177; /Δ=.1441 → .0123 (τ=1).
- Incremental \(R^2\) detectable (N=2000, α=.05, 80%): ≈.004.
- \(\bar p_{50}\) SE: ≈.04; difference SE ≈.057; MDE ≈.16.

## Appendix C — Mixture floor proof details

Let \(\mathcal P_0\) be \(K_\rho\)-closed. Let \(K_{\rm er}\) be key-erasing: \(K_{\rm er}{}_\sharp P_{1,k}=K_{\rm er}{}_\sharp P_0=:Q\). Let \(K_\lambda=\lambda K_{\rm er}+(1-\lambda)K_{\rm id}\). The null member \(Q_\lambda=\lambda Q+(1-\lambda)P_0\in\mathcal P_0\) (closure). The alternative is \(Q_\lambda'=\lambda Q+(1-\lambda)P_{1,k}\). On the \(\lambda\)-fraction the two distributions are identical; on the \(1-\lambda\)-fraction they are \(P_0\) vs \(P_{1,k}\). For any detector \(\phi\),
\[
Q_\lambda(\phi=1)+Q_\lambda'(\phi=0)
=\lambda[Q(\phi=1)+Q(\phi=0)]+(1-\lambda)[P_0(\phi=1)+P_{1,k}(\phi=0)]
\ge\lambda.
\]
Thus \(E^*(\lambda\rho_0)\ge\lambda\). Since \(\lambda=\rho/\rho_0\) is admissible for \(\rho\le\rho_0\), \(E^*(\rho)\ge\rho/\rho_0\). The identity/erased mixture is the extremal attack; a detector that separates the identity fraction attains \(\lambda\), so the bound is tight for this attack. \(\square\)

**Consequence for the original theorem:** at any fixed \(\rho>0\), \(E^*(\rho)\) is bounded below by a positive constant independent of \(n\); the claimed \(E^*\to0\) as \(n\to\infty\) at \(\rho\le c\rho_c\) is false. The only escapes are a uniform/high-probability distortion constraint (excludes the mixture) or a fixed kernel (no minimax). This is the central reason the theorem must be weakened.
