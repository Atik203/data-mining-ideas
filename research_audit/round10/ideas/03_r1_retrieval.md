# R1 — Causal Retrieval Lift (Scoped)

**One-line:** How much does retrieval actually improve an answer, and how much of that lift depends on the answer being present in the retrieved passage?  
**Score:** Core **6.65** / Weighted **6.83** — **CONDITIONAL GO as a bounded measurement paper.**  
**Independence:** 8.5/10.

## What

R1 measures the causal effect of retrieval on answer accuracy with a within-query paired ablation: the same reader answers the same question with the top-1 retrieved passage, a random non-answer passage, or no passage. The primary quantity is \(\tau = E[Y(\text{top-1}) - Y(\text{empty})]\). A second quantity, \(\delta = E[Y(\text{shuffled}) - Y(\text{empty})]\), measures whether a random passage hurts.

## Why

RAG papers often report end-to-end accuracy, but that number confounds retrieval quality, reader ability, and answer leakage. A paired retrieval-lift estimand tells us how much of the performance is actually caused by the retrieved evidence. The answer-presence decomposition explains why retrieval helps some questions and hurts others.

## How

1. Take 200 questions with known answers and a BM25 index.
2. For each question, build three arms: top-1 passage, random non-answer passage, empty context.
3. Ask the same reader with the same prompt and decode settings.
4. Compute paired \(\tau\), \(\delta\), the retrieval-dependent success share \(AF=\tau/E[Y(\text{top-1})]\), and the answer-present/absent decomposition.
5. Scale to N=800 with randomized arm order and three model families.

## Dataset

200 real-corpus questions in the pilot; the scale-up uses N=800. BM25 retriever; Ollama `qwen3.5:4b` in the pilot, with 7B replication planned. The empty arm is **not** a contamination estimate: it also includes reasoning, guessing, and task priors.

## Method

Within-query paired ablation with randomized order — not random assignment. Primary estimand \(\tau\); secondary \(\delta\), \(AF\), \(\tau_{\text{present}}\), \(\tau_{\text{absent}}\), and the interaction. Paired bootstrap and cluster-robust intervals. The scale-up is powered for a 5.5pp \(\tau\) MDE, 2.6pp for \(\delta\), and 7.7pp for the interaction.

## Expected

A positive retrieval lift, a small negative random-passage penalty, and a large answer-presence interaction: retrieval helps when the answer is present and can hurt when it is absent.

## Results

Pilot (200 questions, 700 calls, 0 errors):

| Quantity | Result |
|---|---:|
| Top-1 accuracy | .400 |
| Shuffled accuracy | .000 |
| Empty accuracy | .075 |
| \(\tau\) (top-1 − empty) | +.325, 95% CI [.248, .402] |
| \(\delta\) (shuffled − empty) | −.075, 95% CI [−.112, −.038] |
| Answer present | 109/200 = 54.5% |
| P(EM \| present) | 80/109 = 73.4% |
| P(EM \| absent) | 0/91 = 0% |
| \(\tau_{\text{present}}\) | +.679 |
| \(\tau_{\text{absent}}\) | −.099 |
| \(AF=\tau/E[Y(\text{top-1})]\) | .8125, 95% CI [.704, .901] |

The primary effect is strong and precise. The **twin/conflict extension failed**: v2 twins reached 74% “contains new answer,” below the 90% validity gate; the twin rows were gold-only, so the conflict estimand was never identified. The contamination claim is also not supported by the empty arm.

**Gate B:** conditional GO as a measurement paper. Drop contamination, twins/conflict, Manski/minimax, and “gold” labeling. The closest paper is Maekawa et al. 2024, “Retrieval Helps or Hurts?” (NAACL); Amiraz et al. 2025 is the nearest descriptive ablation. The delta is real but measurement-level.

## Outcome

**Run the scoped N=800 study.** Stop if the 100-item symmetric-prompt check gives \(\tau<10\)pp, or the full run gives \(\tau<5\)pp or no answer-presence interaction. Do **not** submit the contamination or conflict claims. This is a solid 6.6–6.8 paper, not an 8+ novelty paper; use it as the fallback if E2 or RANKCAL fails.
