# Constraint Age or Constraint Load? Separating Two Causes of Multi-Turn Instruction Decay

**Internal short name:** E1

**One-line:** When a model forgets an instruction after several turns, is it because the instruction is old — or because many other constraints are active at the same time?  
**Score:** Core **6.63** / Weighted **7.10** — **REDESIGN DONE; a new pilot is required.**  
**Independence:** 9/10.

## What

E1 separates two explanations for instruction-following decay in multi-turn dialogue:

- **Retention decay:** a constraint becomes less likely to be followed as it ages, even when it is the only active constraint.
- **Load cost:** a constraint is less likely to be followed when many constraints are active at once, even if it was just stated.

The original pilot could not tell these apart because every turn added both one unit of age and one active constraint.

## Why

Multi-turn agents are given standing instructions — tone, format, safety rules, output schema. If the real problem is load, the fix is to restate or prune constraints. If the real problem is age, the fix is to re-anchor old instructions. The two fixes are different, and the literature often reports a single “decay” slope without separating them.

## How

1. Build three arms that vary age and load independently.
2. **PS (persistent single):** one constraint, load 1, ages 0–7. Clean age arm.
3. **FC (fresh cohort):** \(k_t\in\{1,\dots,5\}\) fresh constraints each turn, age 0. Clean load arm.
4. **RR (randomized roster):** one persistent anchor plus \(k_t-1\) fresh fillers; anchor entry randomized independently of the load schedule, so age ⊥ load (\(r=-.03\)).
5. Check every constraint programmatically; cluster by dialogue.

## Dataset

A 60-item bank of programmatically checkable constraints across at least five types (keyword, length, format, JSON key, unit). Fixed 8-turn user scripts, identical across arms; content prompts Latin-squared across turns and dialogues. Three model families: Qwen2.5-7B-Instruct, Llama-3.1-8B-Instruct, Mistral-7B-Instruct-v0.3, all 4-bit on one 24GB GPU.

## Method

GEE logistic regression with turn, arm, and family fixed effects, clustered by dialogue:

\[
\operatorname{logit}E[Y]=\alpha+\beta_a a+\beta_k(k-1)+\tau_t+\gamma_{\text{arm}}+\delta_{\text{family}}
\]

\(\beta_a\) is retention decay at fixed load; \(\beta_k\) is load cost at fixed age; \(\Delta_4=4(\beta_a-\beta_k)\) is the matched contrast. Two 1-df likelihood-ratio tests, a 1-df Wald test for \(\Delta\), and an interaction LRT. Randomization checks must pass before unblinding: \(|r|<.10\), VIF<5, balanced items.

## Expected

A negative \(\beta_a\) means aging hurts; a negative \(\beta_k\) means load hurts. If only load moves, the paper is a load-cost paper. If only age moves, retention is confirmed. If both move, report both and the matched contrast; stop the “dominance” claim if the \(\Delta\) CI is within ±.08.

## Results

**The original pilot is confounded.** Raw slopes were \(\lambda=.461\) (neutral) and .481 (strict), but every turn added another active constraint. Adding turn fixed effects cuts them to **.257** and **.333**, and both dialogue-clustered 95% CIs cross zero (neutral [−.146, .660], strict [−.059, .725]). Exact-constraint fixed effects also leave both CIs crossing zero. Only one model was tested (the “second model” was a stricter prompt), three turns cannot validate a three-parameter exponential law, and 24/22 of 40 constraints were excluded for ceiling/floor.

**Gate D design:** 3 arms × 3 families × 60 dialogues × 8 turns = 540 dialogues / 4,320 generations / 8,638 checks. At 60 dialogues per arm per family, the MDEs are \(\beta_a=.076\) log-odds/turn, \(\beta_k=.060\) per constraint, interaction .039, and \(\Delta=.084\) (power .84 at .10). Cost ≈ **$10–25**, 4–6 GPU-hours.

## Outcome

**Run the new factorial pilot.** It is the only way to know whether the original “turn decay” is real. If both slopes reach the SESOI of .10, the paper reports both and the matched contrast. If neither does, stop E1 (pilot non-replication). Do not scale the old design and do not claim a portable decay law from the current data.
