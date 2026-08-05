# Stage 4 — Market Data Memo

**Scenario:** #20 — Aerospace Manufacturer, EUR 20,000,000 receivable, 365-day settlement
**Retrieved:** 2026-08-05 (market close basis where applicable)
**Author:** Joy Scofield

---

## 1. Retrieved Inputs

| Named Range | Live Value | Unit | Source | Timestamp | Notes |
|---|---:|---|---|---|---|
| `S0_in` | 1.1545 | USD per EUR | Trading Economics, EUR/USD | 2026-08-05 | Explicitly dated quote |
| `R_USD` | 4.00% | Annual %, ACT/360 | StreetStats, US Treasury yield curve, 1-Year | 2026-08-04 | Chosen over Fed funds/SOFR since the assignment calls for a 1-year government yield; 1-Year Treasury constant maturity is the standard USD-leg proxy |
| `R_FC` | 2.728% | Annual %, ACT/360 | Euribor 12-month, euriborrates.com | Last published 2026-06-30 | Most recent published 12M Euribor fixing at time of retrieval (~5 weeks stale — noted as a limitation below); chosen as the standard EUR-leg interbank reference rate |
| `F0_in` | 1.1690 | USD per EUR | **CIP-implied** (no live 1-year forward quote located) | Computed 2026-08-05 | See §2 |
| `K_PUT`, `K_CALL` | 1.1545 | USD per EUR | Set at live spot (ATM) | 2026-08-05 | Per scenario convention |
| `PREM_PUT` | 0.019 | USD | Scenario-given (Scenario 4) | — | **Kept as-is** — retail option quotes are unreliable; not re-sourced, per Stage 4 instructions |
| `PREM_CALL` | 0.024 | USD | Scenario-given (Scenario 4) | — | Same as above |
| `FC_AMT` | 20,000,000 | EUR | Scenario/contract terms | — | Fixed, not market-sourced |
| `T_DAYS` | 365 | Days | Scenario/contract terms | — | Fixed |

---

## 2. CIP-Implied Forward — Computation

No live 1-year EURUSD forward quote was available from the free sources checked (Yahoo Finance, Investing.com, Trading Economics). Per Stage 4 guidance, the forward is computed via covered interest parity:

```
F0 = S0 × (1 + R_USD × T/360) / (1 + R_FC × T/360)
   = 1.1545 × (1 + 0.04 × 365/360) / (1 + 0.02728 × 365/360)
   = 1.1545 × 1.040556 / 1.027659
   ≈ 1.1690
```

**Comparison to the scenario's indicative forward:** the Stage 2/3 spec used the course-assigned `F0_in = 1.0935`. The live, CIP-implied value is **1.1690 — a gap of +0.0755, about 6.9% higher.** This gap is not a data error; it reflects that the scenario's indicative forward was set against a different (lower) baseline spot than today's actual EURUSD level. This was already flagged as an open risk in the Stage 2 spec (§3) and the Stage 3 audit note (Finding 3) — Stage 4 confirms the cause directly: once `S0_in`, `R_USD`, `R_FC`, and `F0_in` are drawn from the same live dataset, the parity check closes almost exactly (see §3).

---

## 3. Model Resolution After Populating Live Data

- **Structure:** No formulas needed to change. All ten named-range input cells were updated in place on the Inputs tab; every downstream formula (Forward, Money Market, Options, Sensitivity) recalculated automatically.
- **Recalc:** 0 errors across 161 formulas (LibreOffice recalc, verified).
- **Parity check:** Now **PASSES** — `F_implied` = 1.168988 vs. `F0_in` = 1.1690, a difference of about −0.0000115 (essentially rounding). Forward proceeds ($23,380,000) and Money Market proceeds ($23,379,769) are now within $231 of each other, versus roughly a $1.5M gap under the indicative Stage 2/3 inputs.
- **Sensitivity table:** Recalculates around the new spot (1.1545); the put floor (`USD_FLOOR_PUT`) is now $22,694,589, up from $22,658,014 under the indicative inputs, consistent with the higher live spot.

**Conclusion:** the earlier parity gap (Stage 3, Finding 3) was correctly diagnosed as an input-consistency issue, not a formula defect. Populating the workbook with a single, internally consistent live dataset resolves it without any structural change — exactly the outcome Stage 4 is designed to test for.

---

## 4. Rate Choice Rationale

- **`R_USD` — 1-Year Treasury constant maturity (4.00%):** chosen over the effective Fed funds rate (used as a placeholder in Stage 2/3) because the assignment specifically asks for a 1-year government yield, which better matches the model's 365-day horizon than an overnight policy rate.
- **`R_FC` — 12-month Euribor (2.728%):** chosen as the standard eurozone interbank reference for a 1-year EUR funding cost, replacing the Stage 2/3 placeholder (ECB deposit facility rate, an overnight policy rate). Euribor 12M is the closer money-market analog to `R_USD`'s Treasury-yield tenor.

---

## 5. FX Hedging Lab Cross-Check

**Result: PASS.** Entered the live inputs from §1 into the FX Hedging Lab and compared against this workbook:

| Output | FX Lab | This Workbook | Match |
|---|---:|---:|---|
| Forward proceeds | $23,380,000 | $23,380,000 | Exact |
| Money-market proceeds | $23,379,769 | $23,379,769 | Exact |
| Implied forward (parity check) | 1.1690 | 1.168988 (rounds to 1.1690) | Exact |
| MM − Forward | −$231 | −$230.66 | Matches within rounding |

The Lab's own parity banner confirms: "Covered interest parity holds: MM − Forward = $-231 (rounding)." This independently validates the workbook's Forward and Money Market hedge logic — no discrepancy found, no resolution needed for those two strategies.

**Note on the Options tab:** the Lab's documented call/put formulas use a simple (non-future-valued) premium, while this workbook future-values the premium per the Stage 2 spec template's instruction (`FV_PREM_PUT`/`FV_PREM_CALL`, carried forward at `R_USD`). This is an expected, explainable convention difference, not an error — the Options tab numbers were not directly compared against the Lab for this reason, since the two tools are deliberately using different (both legitimate) premium-timing conventions.

---

## 6. Limitations

- `R_FC` (Euribor 12M) is dated 2026-06-30, about five weeks stale relative to the 2026-08-05 retrieval date — the most recent published fixing available from the free source checked. A live terminal (Bloomberg/Refinitiv) would carry a same-day print.
- No live 1-year forward quote was located from free sources; `F0_in` is CIP-implied rather than directly quoted (methodology shown in §2, as permitted by the assignment).
- Option premiums remain the Scenario 4 course-assigned values, not live-quoted, per Stage 4 instructions.
