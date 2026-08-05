# Stage 3 — Build Audit Note

**Workbook:** `models/builds/2026-08-05-Scofield-aerospace-model.xlsx`
**Spec:** `docs/specs/2026-07-30-Scofield-aerospace-spec.md`
**Date:** 2026-08-05
**Author:** Joy Scofield

Build method: AI-assisted (Claude), generated directly from the committed Stage 2 spec via `openpyxl`, then audited against the spec's §6.3 checklist and §7 validation rules.

---

## Finding 1 — Broken cross-reference on `USD_FLOOR_PUT`

**What I checked:** Whether the Sensitivity tab's strategy-summary block (`USD_FLOOR_PUT`) correctly pulled the worst-case put outcome from the Options tab.

**What I found:** The Sensitivity tab referenced `Options!D17`, which was a blank cell. The actual `USD_FLOOR_PUT` formula (`=MIN(D6:D16)`) lived one row lower, at `Options!D18` — an off-by-one error introduced when the summary label row was placed two rows below the last grid row instead of one. This caused the summary block to silently show `$0` instead of the real floor value, with no error flag (a blank cell reads as 0 in a SUM/reference, not as `#REF!`).

**What I did:** Corrected the reference to `Options!D18` and re-verified. `USD_FLOOR_PUT` now correctly shows $22,658,014 (matching the put proceeds at $S_T \le K\_PUT$, as expected since the strike is at-the-money).

---

## Finding 2 — Annotation cells accidentally evaluated as formulas

**What I checked:** Ran `recalc.py` after the initial build and inspected every reported error.

**What I found:** Three cells (`Forward!C4`, `MoneyMarket!C8`, `MoneyMarket!C11`) were intended as plain-text formula annotations (e.g., "= FC_AMT × F0_in", written next to the real formula for readability) but had been entered starting with a literal `=`, so LibreOffice tried to parse them as actual formulas and returned `#N/A`.

**What I did:** Rewrote the annotation cells to avoid a leading `=` (e.g., "Formula: FC_AMT x F0_in") and re-ran `recalc.py`, which returned `status: success` with 0 errors across 161 formulas.

---

## Finding 3 — Parity check fails, confirmed as expected (not a build error)

**What I checked:** The Money Market tab's parity check (`F_implied` vs. `F0_in`), per the spec's §7 validation rule that forward and money-market proceeds should be approximately equal.

**What I found:** `F_implied` computes to 1.1684, versus the given `F0_in` of 1.0935 — a gap of about $0.075, or roughly $1.5M in proceeds between the Forward ($21,870,000) and Money Market ($23,367,342) strategies. This is **not a formula error**: it traces directly to the spec's documented assumption (§3) that `F0_in` is course-assigned rather than derived from today's actual `S0_in`, `R_USD`, and `R_FC`. I confirmed this by manually recomputing `F_implied` outside the workbook using the same three inputs and got the same 1.1684 — the formula is correct; the inputs are simply not mutually consistent yet.

**What I did:** Left the check live and visible (it displays "GAP — expected, see Notes tab" rather than a bare fail), and added a note to the Sensitivity tab's "Best Active Hedge" column: because of this gap, Money Market currently dominates the comparison across the entire sensitivity grid — a result that should be treated with caution until Stage 4 loads mutually consistent live data, not read as genuine evidence that Money Market beats Forward.

---

## Finding 4 (bonus) — Kink verification passes

**What I checked:** The spec's kink-verification rule — that put proceeds at $S_T = K\_PUT$ should equal $K\_PUT \times FC\_AMT + FV\_PREM\_PUT$.

**What I found:** At $n=0$ (S_T = S0_in = K_PUT = 1.1526, since the strike defaults to at-the-money), the Options tab returns $22,658,014.42, which matches $1.1526 \times 20{,}000{,}000 - 393{,}985.58$ exactly. Below the strike, the put column is flat at this same value across rows $n=-5$ to $n=0$; above it, proceeds rise linearly with $S_T$ — confirming the payoff kink is correctly located.

**What I did:** No fix needed — recorded as a passing check.

---

## Summary

| # | Finding | Status |
|---|---------|--------|
| 1 | `USD_FLOOR_PUT` off-by-one reference | Fixed |
| 2 | Annotation cells parsed as formulas (`#N/A`) | Fixed |
| 3 | Parity check gap | Confirmed expected, documented |
| 4 | Kink verification | Passing, no action needed |

Final recalc: **0 errors across 161 formulas.**
