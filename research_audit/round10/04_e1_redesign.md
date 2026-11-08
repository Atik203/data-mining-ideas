# E1 Redesign — Separating Constraint Age from Simultaneous Load

**Task D gate.** Date: 8 November 2026. CPU-only design work; no model runs.
Basis: `round9/pilot_idea2_ollama.md`, `round9/pilot_idea2_verify.md`,
`round9/verify_e1_load_confounds.py` / `.json`.

## 1. Why the pilot cannot answer the question

The pilot has exactly `t` active constraints at turn `t`, with lags `0..t-1`
(verified from `obs_ollama_neutral.csv`: turn 1 = lag 0; turn 2 = lags 0,1;
turn 3 = lags 0,1,2). Load is therefore a deterministic function of turn, and
the age distribution is nested inside turn. The independent verification's
turn fixed-effect model is the load-adjusted model:

| Style | pilot lambda (95% CI) | + turn FE | + turn and item FE |
|---|---:|---:|---:|
| neutral | 0.461 (0.170, 0.752), p=.0027 | 0.257 (-0.146, 0.660), p=.145 | 0.405 (-0.093, 0.903), p=.088 |
| strict | 0.481 (0.160, 0.802), p=.0006 | 0.333 (-0.059, 0.725), p=.037 | 0.397 (-0.050, 0.844), p=.035 |

Controlling load/turn removes 31-44% of the raw slope and makes both 95% CIs
cross zero (neutral p=.145, strict p=.037); item FE returns the point estimate
close to the raw value but inflates the SE by 40-70%.
The pilot cannot decide between retention decay, load cost, and generic
turn/context-length effects. A better regression on the same design cannot fix
this: load is turn, age is nested in turn, and the design has almost no
separate identifying variation. The fix is a new factorial.

## 2. Design

**Factors.** Age `a in {0,...,7}` = turns since the constraint was first
stated; it remains in the transcript and is never re-stated. Load
`k in {1,...,5}` = number of constraints stated in the current developer
message and checked this turn. Roster policy is between-dialogue. Model family
is a fixed stratifier. Eight turns per dialogue (>=5 required).

**Arms (3 roster policies x 3 model families x 60 dialogues).**

1. **PS, persistent-single.** One constraint, entry turn `s ~ Uniform{1,...,5}`
   (turns are 1-8), persists to turn 8; `k=1` at every turn. Ages 0-7 at load 1.
   This is the clean retention arm; randomized entry lets turn FE separate age
   from absolute turn, and load is constant by construction.
2. **FC, fresh-cohort.** At each turn `k_t` fresh constraints are stated and
   checked, then retired. `k_t` follows a random permutation of
   `[1,2,3,4,5,1,2,3]` within each dialogue, so load is orthogonal to turn.
   Age is 0 always. This is the clean load arm.
3. **RR, randomized roster.** One persistent anchor with entry
   `s ~ Uniform{1,...,6}`, plus `k_t-1` fresh fillers each turn; `k_t` uses the
   same random permutation. When `k` falls, fillers are retired at random; the
   anchor is never retired and retirement is never performance-based. Within a
   single response the anchor (age `a`) and the fillers (age 0) share the same
   turn, load, and context length; the manipulated difference is age. Position
   in the transcript is part of the age construct, not a separate factor, and
   item identity is randomized. Anchor entry is independent of the load
   schedule, so age is independent of load by construction (simulated
   anchor-level `r=-0.03`).

**Controls.** Fixed 8-turn user script, identical across arms; content prompts
Latin-squared across turns/dialogues so content is not confounded with load.
A 60-item bank of programmatically checkable constraints across >=5 types
(keyword, length, format, JSON key, unit, etc.), pre-screened for mutual
compatibility; items randomized to arm/turn/age and balanced, with item random
effects/FE as a sensitivity. The current developer message lists the active
set; retired constraints are neither re-stated nor checked. Greedy decoding,
fixed max tokens, same chat template for all three families. Response length is
recorded as a post-treatment mediator, not a control.

**Families.** Qwen2.5-7B-Instruct, Llama-3.1-8B-Instruct,
Mistral-7B-Instruct-v0.3, all 4-bit on one 24 GB GPU. A replacement family
(e.g. Gemma-2-9B) must be pre-registered before unblinding.

**Randomization checks (pre-registered; the run is invalid if failed).**
`|corr(anchor age, load)| < 0.10` (simulated -0.03);
`|corr(load, turn)| < 0.10` in both randomized arms (FC 0.011, RR -0.068);
PS entry turns balanced; no item/content imbalance across arms (chi-square
p>.05); VIF of age and load in the primary design matrix <5 (simulated 2.7 and
1.5). In the pilot, load is exactly turn, so with turn FE its VIF is infinite.

**Sample size.** 60 dialogues per arm per family = 540 dialogues, 4,320
generations, 8,638 constraint checks. At ~5 s/generation on one 24 GB GPU this
is ~6 h (3.6 h at 3 s), about $10-25 of rental; checks are CPU-only.

## 3. Model and estimand

Primary analysis is a GEE logistic regression with independence working
correlation and cluster-robust sandwich SEs by dialogue (two-way by dialogue
and item as a sensitivity):

```
M0 (additive): logit E[Y_dtj] = alpha + beta_a * a_dtj + beta_k * (k_dt - 1)
                               + tau_t + gamma_arm + delta_family
M1 (interaction): M0 + beta_x * a_dtj*(k_dt - 1)
```

`Y_dtj=1` if active constraint `j` in dialogue `d`, turn `t`, is satisfied. The
two 1-df LRTs and `Delta` are estimated in M0; M1 is used for the additivity
test and for conditional slopes if that test rejects.

- `beta_a`: marginal log-odds change per turn of constraint age, holding load
  fixed. **Retention decay** if negative.
- `beta_k`: marginal log-odds change per additional simultaneously active
  constraint, holding age fixed. **Load cost** if negative.
- `beta_x`: non-additivity.
- **Matched contrast** at `m=4`: `Delta_4 = 4(beta_a - beta_k) =
  logit p(age=4,k=1) - logit p(age=0,k=5)`. `Delta_4<0` means four turns of
  aging cost more than four extra constraints; `>0` means load dominates.
- Family-specific slopes and a 2-df heterogeneity test. The common slope is
  the stratified (precision-weighted) average; the claim is about these three
  families, not a random family population.

This is a marginal (population-averaged) estimand. Conditional slopes are
~13% larger because of logit-normal dialogue/turn random effects; all MDEs
below are in marginal log-odds.

**Secondary models.** (i) Arm-specific fits: PS age only, FC load only, RR
both. (ii) A conditional logit with dialogue x turn strata and age as the only
covariate, restricted to RR strata containing both an anchor and fresh fillers;
this conditions out load, turn, and dialogue, and does not assume a functional
form for them (item effects are randomized and checked as a sensitivity).
(iii) A within-dialogue permutation test for load using the randomized `k_t`
schedule; under the sharp null of no load effect the load labels are
exchangeable across turns, so the test is exact and immune to turn/context
effects.

## 4. Power and MDE

CPU-only simulation: 400 replicates per cell, 3 families, 8 turns, baseline
`p=.86` at age 0/load 1, dialogue SD 0.8, dialogue-turn SD 0.5, linear turn
drift -0.02, alpha=.05 two-sided, GEE as above. At 60 dialogues per arm per
family:

| Parameter | MDE (80% power) | Power at design alternative |
|---|---:|---:|
| `beta_a` (age) | 0.076 log-odds/turn | 1.00 at -0.25 |
| `beta_k` (load) | 0.060 per constraint | 1.00 at -0.20 |
| `beta_x` (interaction) | 0.039 | 1.00 at 0.08 |
| `Delta = beta_a - beta_k` | 0.084 per unit | 0.84 at -0.10 |
| conditional-logit age | 0.098/turn | 1.00 at -0.25 |
| PS-only age | 0.184/turn | 0.92 at -0.25 |
| FC-only load | 0.084/constraint | 1.00 at -0.20 |

At `p=.86` the age MDE over four turns is 0.30 log-odds (~4.1 pp), the load MDE
over four constraints is 0.24 log-odds (~3.2 pp), and the `Delta_4` MDE is 0.33
log-odds (~6 pp). These are conservative relative to the pilot's raw
0.46-0.48/turn. Null rejection rates at D=60 are .043 (age), .042 (load), .053
(interaction) over 600 replicates, so the tests are calibrated. MDEs are stable
across dialogue SD 0.5-1.2 (age 0.078 vs 0.076; load 0.062 vs 0.059): higher
heterogeneity attenuates the marginal effect but leaves the SE essentially
unchanged. Item random effects (SD 0.5-0.9), item FE, and two-way
(dialogue x item) clustering change the MDEs by <8% (age 0.076-0.082; load
0.058-0.062), so item reuse in the 60-item bank is not a material threat.

**Sample-size alternatives.** D=40: age MDE 0.094, load 0.074, `Delta` 0.100,
power for `Delta=0.10` is 0.64. D=30: 0.113/0.085, `Delta` power 0.51. D=60 is
recommended; D=40 is the minimum and is underpowered for the dominance
question.

**Identification check.** With no random effects, the primary model recovers
`beta_a=-0.250` (true -0.25) and `beta_k=-0.200` (true -0.20) *with turn FE*.
Unlike the pilot, turn FE does not absorb either factor, because age and load
are separately randomized.

## 5. The exact test that distinguishes retention from load

The pilot's test was "lambda with vs without turn FE"; because load equals turn
and age is nested in turn, that test had no separate identifying variation. The
new discriminator is a **joint two-factor test in one model**, with three
pre-registered pieces:

1. **Two 1-df likelihood-ratio tests in M0.** `H0_age: beta_a=0` with `beta_k`
   free: does age add information given load? `H0_load: beta_k=0` with `beta_a`
   free: does load add information given age? M0 assumes additivity; the
   interaction LRT below is the gate for that assumption. Because the design
   orthogonalizes the factors (anchor-level `|r|<0.10`; load-turn `|r|<0.07`;
   VIF<5), these are distinct tests. In the pilot the age and load tests were
   not separately identified, which is exactly why the turn-FE model collapsed.
2. **The `Delta` test.** A 1-df Wald z for `H0: beta_a - beta_k = 0` in M0,
   using the cluster-robust covariance; report `Delta_4=4Delta` and the
   probability-scale version. This is the direct "which mechanism per unit"
   test.
3. **The interaction LRT.** Compare M0 with M1; `H0: beta_x=0` (1 df). If
   rejected, the additive decomposition is invalid: report `beta_a` at k=1 and
   k=5 and `beta_k` at a=0 and a=4 from M1, and stop the single-`Delta` claim.

**Assumption-light confirmations.** Age: the dialogue x turn conditional logit
above (MDE 0.098/turn; power 1.00 at -0.25). Load: the randomized-load
within-dialogue permutation test. Both are reported alongside the GEE; the GEE
is primary.

**Pre-registered decision table.**

| `beta_a` | `beta_k` | Conclusion |
|---|---|---|
| < -0.10 (CI upper < 0) | ~0 (CI lower > -0.10) | retention decay |
| ~0 | < -0.10 (CI upper < 0) | load cost |
| < -0.10 | < -0.10 | both; `Delta` says which per unit |
| ~0 | ~0 | pilot non-replication; stop E1 |
| interaction != 0 | - | conditional curves; no additive decomposition |

A significantly positive slope is reported as facilitation and is not forced
into the decay/cost labels. "Present" = point estimate <= -0.10 and 95% CI
upper bound < 0; "absent" = 95% CI lower bound > -0.10; otherwise inconclusive. The stronger claim that the
effect exceeds the SESOI requires the 95% CI upper bound < -0.10 and is
reported separately, not used as the primary gate. The SESOI of 0.10 log-odds
per unit is OR 0.905 per turn/constraint, about 4-6 pp over four units, the
smallest effect that would change the theoretical claim.

## 6. Go/no-go

**GO to the run.** The design gate passes: >=80% power at the SESOI for both
slopes and the matched contrast, age and load orthogonal by construction, and
a cost of 4,320 generations (~4-6 h, $10-25). D=60 per arm per family is the
recommended size.

**Stop conditions after the run.**
- Validity failure (randomization checks or VIF) -> do not interpret; fix and
  rerun.
- Neither slope present beyond the SESOI -> pilot non-replication; stop the E1
  line.
- Only load present -> reclassify the pilot as load cost; drop the retention
  claim.
- Only age present -> retention confirmed; drop the load-cost claim.
- Both present, `Delta` CI within +/-0.08 -> report "both, indistinguishable
  per unit"; stop the dominance question.
- Interaction present -> report conditional curves; stop the additive
  decomposition.
- Family heterogeneity (>=2/3 families opposite sign) -> do not claim a
  family-general effect; report per family.

**Path.** This file:
`E:\PROJECT\Research-Ideas\research_audit\round10\04_e1_redesign.md`.
If GO, the next artifact is `round11/e1_prereg.yaml` (exact bank, schedules,
seeds), then the run.

## Final (<=300 words)

**Design.** 3 arms x 3 families x 60 dialogues x 8 turns = 540 dialogues /
4,320 generations. PS: one persistent constraint, load 1, ages 0-7. FC:
`k_t in {1..5}` fresh constraints per turn, age 0. RR: one persistent anchor
plus `k_t-1` fresh fillers, anchor entry randomized independently of load, so
age is independent of load (r=-0.03) and load is independent of turn (|r|<0.07).
Constraints are stated once, never repeated; checks are programmatic, clustered
by dialogue.

**Estimand.** GEE logistic (M0 additive; M1 adds age x load), turn FE, arm FE,
family FE. `beta_a` = retention decay per turn at fixed load; `beta_k` = load
cost per constraint at fixed age; `Delta = beta_a - beta_k`, matched at 4 turns
vs 4 constraints.

**MDE.** At D=60/arm/family, 80% power, alpha=.05: `beta_a` 0.076
log-odds/turn; `beta_k` 0.060; interaction 0.039; `Delta` 0.084/unit (power
0.84 at 0.10). Conditional-logit age MDE 0.098/turn. D=40: 0.094/0.074,
`Delta` power 0.64; D=30 is underpowered.

**Exact test.** Two 1-df LRTs (age|load, load|age) in additive M0, the 1-df
Wald test of `Delta`, and the interaction LRT (M0 vs M1). The pilot failed because load
equalled turn and age was nested in turn, so turn FE absorbed it (lambda 0.46
-> 0.26/0.33, CIs crossing 0). The new turn FE is identified: synthetic checks
recover `beta_a=-0.250` and `beta_k=-0.200` with turn FE. Assumption-light
confirmations: dialogue x turn conditional logit for age; randomized-load
permutation test for load.

**Go/no-go.** GO if randomization checks pass (|r|<0.10, VIF<5) and both slopes
reach SESOI 0.10; stop E1 if neither does. Only load -> pilot is load cost.
Only age -> retention confirmed. Both -> report both and `Delta`; stop if its
CI is within +/-0.08. Cost ~$10-25, 4-6 h.

**Path.** `E:\PROJECT\Research-Ideas\research_audit\round10\04_e1_redesign.md`.
