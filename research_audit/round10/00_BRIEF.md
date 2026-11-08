# Round 10 — Next-Gate Brief (CPU-only)

**Date:** 8 November 2026.  
**Basis:** `round9/FINAL_SURVIVORS_REVIEW.md` (surviving-only, evidence-adjusted).  
**Rules:** CPU only. No GPU, no Ollama, no model loads, no HF downloads, no paid API. Use Python/stdlib/numpy/scipy already installed. Do not re-run any pilot. Do not edit other agents' files. Write only your assigned report.

**Current survivor scores (Round-8 rubric; Core = mean Nov/Method/Math/Exp):**

| Idea | Core | Weighted | Status |
|---|---:|---:|---|
| T1 watermark | 7.63 | 7.73 | white-box plant pass; ρ_c theorem unproved |
| E2 CTO | 7.38 | 7.55 | n=60 screening, p=.0625 |
| R1 retrieval lift | 7.38 | 7.43 | strong lift; twins fail (74%) |
| RANKCAL | 6.75 | 7.18 | directional only, CIs cross 0 |
| E1 turn retention | 6.63 | 7.10 | age confounded with active load |
| Tool-IV | 6.63 | 6.98 | marginal; LATE CI crosses 0 |
| M1 script | 6.38 | 6.73 | directional; p=.052/.127 |

**Global decision rule:** a gate passes only if it produces a concrete, falsifiable next experiment with power/MDE, a valid estimand, and a stop condition. No score inflation.

---

## Task A — T1 theorem gate

Read `round9/pilot_idea1_whitebox.md` and `round8/05_deep_theory_85.md` T1. Answer: can the semantic-radius → green-status contraction assumption be formalized and proved, or must the theorem be weakened? Derive the missing lemma or give a counterexample. Specify the exact next experiment (δ, n, paraphrase levels, N per cell, MDE) and a stop condition. Output `round10/01_t1_theorem_gate.md`.

## Task B — R1 scoped paper gate

Read `round9/pilot_idea8_realcorpus.md`. Rewrite the claim set to randomized retrieval lift/passage reliance only. Recheck the 5 closest functional papers (descriptive ablations, RAG attribution, contamination tests) and confirm the delta. Design a valid NER type-matched twin construction with a ≥90% validity protocol, or recommend dropping the conflict claim. Compute what N=800 buys (MDE) and outline the paper. Output `round10/02_r1_scoped_paper.md`.

## Task C — E2 scale-up gate

Read `round9/pilot_idea5.md`. Design the confirmatory study: ≥500 real prefixes, real tool outputs, 4 severity levels, multiple models/tasks. Give power/MDE for the dose-response slope and recovery half-life, prefix-harvest protocol, label validation, compute/cash. Decide GO/NO-GO under $70 and a 24GB rental. Output `round10/03_e2_scaleup.md`.

## Task D — E1 redesign gate

Read `round9/pilot_idea2_ollama.md`, `round9/pilot_idea2_verify.md`, and `round9/verify_e1_load_confounds.py`/`.json`. Design a factorial that independently varies constraint age and number of simultaneously active constraints (persistent-single constraint arm, randomized additions/replacements, ≥5 turns, multiple model families). Give the model, estimand, power/MDE, and the exact test that distinguishes retention decay from load cost. Output `round10/04_e1_redesign.md`.

## Task E — RANKCAL + Tool-IV fixes gate

Read `round9/pilot_idea10.md` and `round9/pilot_idea7.md`. For RANKCAL: coarser pre-registered groups, N≥1500 power for ΔAUROC, TOST/equivalence framing. For Tool-IV: correct Balke–Pearl/one-sided sharp bounds, harder tasks to escape ceiling, stronger first stage, placebo-nudge exclusion sensitivity. Give exact next-run specs, MDE, and stop conditions. Output `round10/05_rankcal_tooliv_fixes.md`.

## Task F — M1 scale-up gate

Read `round9/pilot_idea4.md` and `round9/pilot_idea4/SPOTCHECK_REPORT.md`. Decide: is η theory needed, or is this an empirical transfer paper? Design the 7B/9k-triple confirmation with power for the native→roman gap and alignment gain, native-review protocol, and a stop condition. Output `round10/06_m1_scaleup.md`.

**Time:** each gate is 45–90 min CPU work; all six run in parallel.
