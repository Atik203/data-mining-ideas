# TODO — Data-Mining Track

Last updated: 8 November 2026.

## Complete

- [x] All six Round-10 gates: T1 theorem, R1 scoped paper, E2 scale-up, E1 redesign, RANKCAL/Tool-IV fixes, M1 scale-up
- [x] Final eight-factor ranking and recommendation — `research_audit/round10/index.md`
- [x] Seven full-title idea pages — `research_audit/round10/ideas/`
- [x] Survivor review and independent pilot checks — `research_audit/round9/FINAL_SURVIVORS_REVIEW.md`

## Next — execute E2

- [ ] G0: harvest 880 eligible τ-bench prefixes, audit 40, replay-verify ≥98%, freeze 800 (CPU, ~2 days, $0)
- [ ] G1: 60-prefix calibration on 3 models (GPU, ~3 h, ~$2); pass parse ≥95%, placebo jitter ≤20%, s=3 switch 10–80%
- [ ] Main run: 35–45 GPU-h, $15–22; stop at $22 or 45 GPU-h
- [ ] Labels: 160-item double-coding, κ ≥ .80
- [ ] Analysis: conditional-logit slope; recovery half-life; pre-registered claim hierarchy

## Fallback

- [ ] RANKCAL: 2,500 MMLU items; Qwen2.5-7B base/Instruct + Llama-3.1-8B; paired DeLong; TOST ±0.03

## Later / conditional

- [ ] E1: run the new 3-arm × 3-family × 60-dialogue × 8-turn factorial ($10–25)
- [ ] Tool-IV: 400-item calibration, then 4,500 calls on hard tasks
- [ ] M1: native-speaker audit (~$320) before any 9k/7B run
- [ ] T1: do not run the old 7B phase-boundary experiment; measurement note only

## Rules

- One paper, one budget. Do not run E2 and RANKCAL at the same time.
- Rent a 24GB GPU only after G0/G1 (E2) or calibration (RANKCAL/Tool-IV) passes.
- No broad scan without a new theorem, intervention, or dataset.
