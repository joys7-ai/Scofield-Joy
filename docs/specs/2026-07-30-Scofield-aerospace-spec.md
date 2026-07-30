<div style="border-top: 6px solid #024731; border-bottom: 1px solid #B2B2B2; padding: 12px 0; margin-bottom: 24px; font-family: 'Open Sans', Helvetica, Arial, sans-serif;">
  <div style="color: #024731; font-weight: 700; letter-spacing: 0.06em; text-transform: uppercase; font-size: 0.85rem;">University of Hawaiʻi at Mānoa · Shidler College of Business</div>
  <div style="color: #000000; font-weight: 700; font-size: 1.25rem; margin-top: 4px;">FIN-321 International Finance &amp; Securities</div>
  <div style="color: #525252; font-weight: 400; font-size: 0.95rem;">FX Transaction Hedging Project — Technical Specification</div>
</div>

# Aerospace Manufacturer, Inc. — FX Transaction Hedge Model · Technical Specification

> <span style="color:#024731; font-weight:700;">Technical specification</span> for the FX transaction hedge model — the named-range contract, calculation flow, and validation checks, precise enough that an AI or a colleague could build (or rebuild) the workbook from this document alone. This spec is the input the AI-assisted build works from.

| Field | Value |
|------|------|
| **Created by** | Joy Scofield |
| **Updated by** | Joy Scofield |
| **Date Created** | 2026-07-30 |
| **Date Updated** | 2026-07-30 |
| **Version** | 0.1 |
| **LLM Used** (optional) | Claude — drafted from Stage 1 memo + assigned scenario; corrected FC_AMT currency error and reconciled with course-standardized named ranges (see prompt-log.md) |
| **Role** | Treasury Analyst |
| **Audience** | CFO / Director of Treasury |
| **Companion Workbook** | Student build (Stage 3) |

---

## 1. Problem Statement

Aerospace Manufacturer, Inc. expects a EUR 20,000,000 receivable settling in 365 days from an export contract. A depreciation in EURUSD over that horizon would reduce realized USD proceeds and compress margin on the contract. This specification documents the analytical framework used to quantify and compare four strategies — **no hedge**, **forward hedge**, **money-market hedge**, and **option (put) hedge** — and to produce the sensitivity evidence that supports the Stage 4 hedging recommendation.

- **Exposure type:** Receivable, functional currency USD
- **Foreign-currency amount:** EUR 20,000,000, quoted USD per EUR
- **Settlement:** 365 days from today (2026-07-30)
- **Objective:** Protect USD value of the receivable while comparing the cost of certainty (forward/MM) against the cost of optionality (put floor)
- **Decision context:** Corporate treasury recommendation to CFO, prior to any hedge execution

---

## 2. Inputs (Known Variables)

### 2.1 Core Inputs

| Named Range | Description | Placeholder Value | Unit | Stage-4 Data Source |
|-------------|-------------|-------------------:|------|----------------------|
| `FC_AMT` | Foreign-currency receivable | 20,000,000 | EUR | Contract terms (fixed, not market-sourced) |
| `S0_in` | Spot rate at inception | 1.1526 | USD per EUR | Indicative — EURUSD spot, Investing.com, accessed 2026-07-30; replaced with live ECB/Bloomberg reference rate at Stage 4 |
| `F0_in` | Forward rate to settlement | 1.0935 | USD per EUR | Course-assigned (Scenario 4, Aerospace); replaced with live 1-year forward quote at Stage 4 |
| `R_USD` | USD interest rate to settlement | 3.63% | Annual %, ACT/360 | Indicative — effective Fed funds rate, accessed 2026-07-30; replaced with live Fed H.15/SOFR data at Stage 4 |
| `R_FC` | EUR interest rate to settlement | 2.25% | Annual %, ACT/360 | Indicative — ECB deposit facility rate (eff. 17 Jun 2026), accessed 2026-07-30; replaced with live ECB rate at Stage 4 |
| `T_DAYS` | Days to settlement | 365 | Days | Contract terms (fixed) |
| `BASIS` | Day-count denominator (both legs) | 360 | Days | ACT/360 — applied uniformly per the Stage 2 assignment's worked money-market formula |
| `K_PUT` | Put option strike | 1.1526 | USD per EUR | Set at spot (ATM) per course guidance; confirmed/replaced with live quote at Stage 4 |
| `PREM_PUT` | Put premium, USD per 1 EUR | 0.019 | USD | Course-assigned (Scenario 4); replaced with live quote at Stage 4 |
| `K_CALL` | Call option strike (reference only — not the primary hedge for a receivable) | 1.1526 | USD per EUR | Set at spot (ATM); replaced with live quote at Stage 4 |
| `PREM_CALL` | Call premium, USD per 1 EUR (reference only) | 0.024 | USD | Course-assigned (Scenario 4); replaced with live quote at Stage 4 |

### 2.2 Derived / Intermediate Values

| Name | Description | Formula |
|------|-------------|---------|
| `DF_USD` | USD accumulation factor to settlement | `1 + R_USD × T_DAYS / BASIS` |
| `DF_FC` | EUR accumulation factor to settlement | `1 + R_FC × T_DAYS / BASIS` |
| `FV_PREM_PUT` | Future value of put premium at settlement | `−PREM_PUT × FC_AMT × DF_USD` |
| `S_T_grid` | Sensitivity spot grid at settlement | `S0_in × (1 + n × STEP_FRAC)` for `n = −5…+5`, `STEP_FRAC = 1%` |
| `USD_NO_HEDGE` | USD proceeds under no hedge | `S_T × FC_AMT` |

---

## 3. Assumptions & Constraints

- **Quote convention:** All rates expressed as USD per EUR. A higher quote means EUR appreciation.
- **Horizon:** Single-maturity model; `T_DAYS = 365`.
- **Day-count basis:** `BASIS = 360` (ACT/360), applied to both the USD and EUR legs — matching the Stage 2 assignment's explicit worked formula (`R_FC × T_DAYS/360`, `R_USD × T_DAYS/360`), rather than a split USD/EUR convention.
- **Parity:** The money-market hedge is assumed to replicate the forward hedge under covered interest-rate parity. Because `F0_in` in this exercise is course-assigned rather than derived from `S0_in`, `R_USD`, and `R_FC`, the parity check (§4, Step 3) is expected to show a gap at Stage 2 — this becomes a live, closable check only once Stage 4 replaces all four inputs with mutually consistent market data.
- **Option premium:** Paid upfront in USD, quoted per 1 unit of EUR (no contract multiplier). Carried forward at `R_USD` to `FV_PREM_PUT` so it's on the same footing as settlement-date proceeds.
- **Counterparty / credit risk:** Excluded.
- **Transaction costs & bid-ask spreads:** Excluded from the base case; flagged as a sensitivity candidate in §6.2.
- **Tax / accounting treatment:** Excluded. Pre-tax cash outcomes only.
- **Scenario construction:** `S_T` varied deterministically across a grid; no probability weights applied.

---

## 4. Calculation Flow

### Step 1 — Derived inputs

1. `DF_USD` = `1 + R_USD × T_DAYS / BASIS`
2. `DF_FC` = `1 + R_FC × T_DAYS / BASIS`
3. `FV_PREM_PUT` = `−PREM_PUT × FC_AMT × DF_USD`

### Step 2 — Forward hedge (certainty benchmark)

- `USD_FWD` = `FC_AMT × F0_in`
- Locked-in at t₀; invariant across the `S_T` grid.

### Step 3 — Money-market hedge (parity check)

1. Borrow `FC_AMT / DF_FC` euros today.
2. Convert to USD at spot: `(FC_AMT / DF_FC) × S0_in`.
3. Invest the USD to maturity: `USD_MM` = `(FC_AMT / DF_FC) × S0_in × DF_USD`.

> **Parity check:** `USD_MM ≈ USD_FWD` within rounding. Expected NOT to hold closely at Stage 2 (see §3) — a persistent gap once live Stage-4 data is loaded, by contrast, would flag a formula error.

### Step 4 — Option hedge (put floor)

- Pay `PREM_PUT × FC_AMT` USD today for a put on EUR at strike `K_PUT`.
- For each `S_T` on the grid: `USD_PUT(S_T)` = `MAX(S_T, K_PUT) × FC_AMT + FV_PREM_PUT`

### Step 5 — Sensitivity table (rows of the grid)

| Column | Output | Formula |
|--------|--------|---------|
| No hedge | `USD_NO_HEDGE(S_T)` | `S_T × FC_AMT` |
| Forward | `USD_FWD` | constant across rows |
| Money market | `USD_MM` | constant across rows |
| Option (put) | `USD_PUT(S_T)` | `MAX(S_T, K_PUT) × FC_AMT + FV_PREM_PUT` |
| Hedge profit | `HEDGE_PROFIT_k(S_T)` | `USD_k − USD_NO_HEDGE`, one sub-column per strategy |
| Best active hedge | label | `ARGMAX(USD_FWD, USD_MM, USD_PUT)` |

### Step 6 — Summary metrics (scalar outputs)

- `USD_FLOOR_PUT` = `MIN(USD_PUT)` across `S_T_grid` (worst-case put outcome on the grid)
- `USD_BASE_k` = `USD_k` evaluated at `S_T = S0_in`, for each strategy

---

## 5. Outputs

| Output | Description | Format | Purpose |
|--------|-------------|--------|---------|
| Input panel | All named-range inputs with units, sources, and access dates | Top of each tab | Single source of truth |
| Strategy summary | `USD_FWD`, `USD_MM`, `USD_BASE_k` per strategy, plus `USD_FLOOR_PUT` | Table above sensitivity grid | Executive at-a-glance |
| Sensitivity table | USD proceeds for each strategy across `S_T_grid` ±5% | Table | Core analytical evidence |
| Hedge-profit columns | `USD_k − USD_NO_HEDGE` per strategy per row | Sub-table | Isolates hedge value-add |
| Winner label | `ARGMAX` per row | Label column | Quick-read decision cue |
| Sensitivity chart | Line chart of USD outcome vs. `S_T`, all strategies | Embedded chart | Visual comparison |

### 5.1 Computed Base-Case Values (illustrative, at `S_T = S0_in = 1.1526`)

| Strategy | USD Proceeds | Hedge Profit vs. No Hedge |
|----------|--------------:|---------------------------:|
| No hedge | $23,052,000 | — |
| Forward | $21,870,000 | −$1,182,000 |
| Money market | ~$23,369,000* | +$317,000* |
| Option (put) | $22,658,014 | −$393,986 |

*Money-market figure reflects today's actual market rates against the course-assigned `F0_in`, not a closed parity relationship — see §3 and §4 Step 3. This gap should not persist once Stage 4 loads mutually consistent live data.

---

## 6. Model Review — What Worked & What to Improve

### 6.1 What Worked

- Four-strategy comparison (no hedge, forward, money market, option) priced against one `S_T` grid, making the trade-off immediate.
- Put payoff vectorized across the grid via `MAX(S_T, K_PUT)`.
- Baseline marker at `S_T = S0_in` anchors the scenario range to a recognizable reference point.

### 6.2 What to Improve (known-risks register for the Stage 3 build)

- **Named-range discipline must be complete from the start.** Every input in §2.1 needs its own named range — no `$F$n`-style hardcoded cell references anywhere in the calculation flow.
- **Day-count basis is a simplification.** This spec uses one shared `BASIS = 360` for both legs, per the assignment's worked formula. In practice, EUR money-market convention is typically ACT/365, not ACT/360 — a more rigorous future build could split `BASIS_USD`/`BASIS_FC`, but that's a deliberate scope decision here, not an oversight.
- **Sensitivity step size must be driven by one `STEP_FRAC` input** (1%), not hard-coded per tab, so the ±5% range stays consistent.
- **Strike defaulting should be explicit.** `K_PUT` defaults to `S0_in` (ATM) here — the workbook should expose this as an override, not bury it.
- **Report both a floor and a baseline for the option.** `USD_FLOOR_PUT` (worst case) and `USD_BASE_k` at `S_T = S0_in` should both appear in the strategy summary — a floor alone hides the ATM outcome.
- **No transaction-cost sensitivity yet.** Add a bid-ask/commission knob on the forward and a spread on the option premium in a later iteration; real treasury desks never see the mid.
- **Money-market walk should stay to three explicit steps** (borrow, convert, invest) — resist collapsing it into one nested formula, since the audit trail is the point (see Step 3 above).

### 6.3 Auditability Checklist

- [ ] Every input has a named range from §2.1
- [ ] Every formula in §4 uses named ranges — no bare cell references
- [ ] Money-market hedge ties to forward hedge within rounding once live Stage-4 data is loaded (parity check)
- [ ] Put payoff at `S_T = K_PUT` equals `K_PUT × FC_AMT + FV_PREM_PUT` (kink verification)
- [ ] Sensitivity grid is symmetric around `S_T = S0_in` and driven by `STEP_FRAC`
- [ ] Notes tab records spot/forward/rate sources with access dates
- [ ] Cell colors match the legend: yellow inputs, blue assumptions, black formulas, green cross-tab links

---

## 7. Sensitivity Plan

- **Grid:** `S_T_grid` spans `S0_in × (1 ± 5%)` in 1% increments → 11 rows including the baseline.
- **Strategies plotted:** no hedge, forward, money market, option (put).
- **Primary chart:** line chart, `S_T` on x-axis, USD proceeds on y-axis. Forward and money-market series are horizontal by construction; no-hedge is a straight line through the origin; option is piecewise-linear with a kink at `K_PUT`.
- **Secondary table:** hedge profit vs. no hedge for each strategy.
- **What the chart should communicate:** the trade-off between certainty (forward/money-market, flat lines), optionality (put, kinked payoff), and naked exposure (no hedge, unbounded both directions).

---

## 8. Limitations & Next Steps

**Limitations.** This specification does not incorporate:
- Partial/layered/dynamic hedging (treated as static, full-notional hedge at t₀)
- Credit, counterparty, and settlement risk
- Implied-volatility-based option pricing (premia are scenario inputs, not Black-Scholes outputs)
- Hedge accounting treatment (ASC 815/IFRS 9)
- Multi-currency or multi-horizon portfolio effects
- Reconciliation of the course-assigned `F0_in` against actual market parity (flagged, not resolved, at Stage 2)

**Next steps — Stage 3/4 will:** (a) build the workbook from §4 using an AI prompt with this spec as the instruction block, (b) audit the build against the §6.3 checklist, (c) replace all indicative inputs with live, sourced, timestamped data, and (d) translate the sensitivity evidence into the CFO recommendation memo.

---

## Appendix A — Change Log

| Version | Date | Author | Change |
|---------|------|--------|--------|
| 0.1 | 2026-07-30 | Joy Scofield | Initial draft |

---

## Appendix B — Brand & Formatting Standards

All FIN-321 deliverables — this spec, the companion workbook, the Stage 4 memo, and any derivative chart or slide — must conform to the **University of Hawaiʻi at Mānoa Brand Style Guide** as codified in [`docs/_branding/design.json`](../../../../../docs/_branding/design.json) (v1.0.0). The tokens below are a reader-friendly extract; the JSON file is the source of truth.

### B.1 Color Palette

**Primary colors — logos, headings, accents. Do not substitute.**

| Token | Hex | RGB | CMYK | Pantone | Usage |
|-------|-----|-----|------|---------|-------|
| UH Green | `#024731` | 2, 71, 49 | 93, 24, 85, 68 | 3435 C | Logos, H1/H2, key UI accents, primary buttons |
| Black | `#000000` | 0, 0, 0 | 0, 0, 0, 100 | Process Black | Body text, borders, maximum-contrast UI |

**Secondary colors — borders, muted elements, backgrounds.**

| Token | Hex | RGB | Pantone | Usage |
|-------|-----|-----|---------|-------|
| Silver | `#B2B2B2` | 178, 178, 178 | Cool Gray 5 C | Subtle borders, rules, disabled states |
| White | `#FFFFFF` | 255, 255, 255 | — | Page backgrounds, inverse text, cards |

**Extended light-mode tokens (for tables, callouts, footnotes).**

| Purpose | Token | Hex |
|---------|-------|----:|
| Secondary text / captions | Neutral-600 | `#525252` |
| Tertiary text | Neutral-500 | `#737373` |
| Hover / pressed state | UH Green 700 | `#013D26` |
| Link text | Light-mode link | `#024731` |
| Tint / callout fill | UH Green 50 | `#E6F2EF` |
| Table border (subtle) | Neutral-200 | `#E5E5E5` |

**Status colors (informational banners only — never for body text).**

| State | Text | Background | Solid |
|-------|-----:|-----------:|------:|
| Success / Info | `#024731` | `rgba(2, 71, 49, 0.08–0.12)` | `#024731` |
| Warning | `#737373` | `rgba(178, 178, 178, 0.20)` | `#B2B2B2` |
| Error | `#B43232` | `rgba(180, 50, 50, 0.12)` | `#8B2727` |

> **Prohibited:** custom palettes, gradients, red body type, non-ADA contrast combinations, or layouts too dark for print legibility. If a color is not in this appendix or `design.json`, it is not brand.

### B.2 Typography

| Element | Web (screen) | Print | Fallback | Weight |
|---------|--------------|-------|----------|-------:|
| H1 / H2 | Open Sans Bold | Avenir Bold | Helvetica, Arial | 700 |
| H3 / H4 | Open Sans Semibold | Avenir Bold | Helvetica, Arial | 600 |
| Body | Open Sans Regular | Avenir Book | Helvetica, Arial | 400 |
| Caption / footnote | Open Sans Regular | Avenir Book | Helvetica, Arial | 400 |
| Monospace (formulas, named ranges, code) | `ui-monospace` | Consolas | monospace | 400 |

**Sizing & setting rules:**

- Body type minimum **10 pt**; use **11–12 pt** for any printed copy intended for faculty, committees, or older audiences.
- **Leading** (line-height): 3–5 pt greater than type size on printed copy.
- **Alignment:** flush left, ragged right for body copy. Never center or fully justify body text.
- **Headlines:** Avenir Bold in ALL CAPS for emphasis, or sentence case for restrained look. Pick one and stay consistent within a deliverable.

### B.3 Applying the Palette in the Workbook

| Element | Color | Hex | Notes |
|---------|-------|----:|-------|
| Section headings & banners (Input / Sensitivity / Outputs) | UH Green | `#024731` | Bold, 12 pt |
| Input cells (editable by analyst) | Yellow fill | `#FFFF00` | Standard-industry input color; flagged as **Excel-only** — not a brand color |
| Assumption cells (analyst scenario knobs) | Blue text | `#0000FF` | Standard-industry hardcode color |
| Formula cells | Black text | `#000000` | All calculations |
| Cross-tab links | UH Green text | `#024731` | Mirrors brand primary |
| External links (e.g., Bloomberg pulls) | Dark red text | `#B43232` | Used sparingly; never for commentary |
| Table gridlines / separators | Silver | `#B2B2B2` | 0.5 pt |
| Header row fill | UH Green | `#024731` | White text |

**Workbook typography:** set the workbook default font to **Open Sans** (fall back to Arial where Open Sans is unavailable). Do not use Calibri.

### B.4 Charts (Sensitivity Line Chart — §7)

| Series | Line color | Style | Weight |
|--------|-----------:|-------|-------:|
| No hedge | `#000000` (Black) | Solid | 1.5 pt |
| Forward hedge | `#024731` (UH Green) | Solid | 2.0 pt |
| Money-market hedge | `#013D26` (UH Green 700) | Dashed | 1.5 pt |
| Option hedge | `#525252` (Neutral-600) | Dotted | 2.0 pt |

- **Gridlines:** Silver `#B2B2B2`, 0.5 pt, horizontal only.
- **Axis labels & title:** Open Sans Semibold, black, 10 pt minimum.
- **Legend:** top or right, Open Sans Regular, 10 pt.
- **No 3-D effects, no drop shadows, no gradient fills.**

### B.5 Accessibility & Prohibited Practices

- Every text/background combination must clear **ADA AA contrast** (4.5:1 for body, 3:1 for large text). Check UH Green on white (✓ 11.5:1) and UH Green on silver (✗ 2.1:1 — do not use).
- **Never** use red type for body or primary content. Dark red `#B43232` is permitted only for error-state banners and external-link markers in the workbook.
- **Never** layer body copy on dark backgrounds — dark-mode tokens are reserved for digital product UI, not print or PDF deliverables.
- **Never** introduce custom palettes, gradients, or alternative brand marks. If a need arises, escalate via the UH Mānoa Branding and Marketing Office (`branding@hawaii.edu`, `(808) 956-3598`).

### B.6 File & Deliverable Conventions

- **Spec file name:** per the Stage 2 assignment page, `docs/specs/YYYY-MM-DD-{lastname}-{scenario-slug}-spec.md` — used for this deliverable in place of the template's own `stage3-spec-LASTNAME.md` convention, since the assignment page's naming rule is what governs submission.
- **Workbook file name:** keep the Stauffer template's convention — `FIN 321 - Chapter 8 Transaction Hedging_[YEAR]_LASTNAME.xlsx`.
- **PDF export of the spec:** embed fonts; render at Letter size; margins ≥ 0.75 inch; body 11–12 pt.
- **All files** must carry the UH Mānoa banner block (see top of this template) and the brand footer (below).

---

<div style="border-top: 1px solid #B2B2B2; padding-top: 8px; margin-top: 24px; font-family: 'Open Sans', Helvetica, Arial, sans-serif; font-size: 0.8rem; color: #525252;">
  Prepared per UH Mānoa brand standards (<code>docs/_branding/design.json</code> v1.0.0). Primary green <code>#024731</code> · Black <code>#000000</code> · Silver <code>#B2B2B2</code> · Body type Open Sans Regular, 11–12 pt for printed copies · ADA-compliant contrast · Flush-left, ragged-right alignment · No red body type · No custom palettes or gradients.
</div>
