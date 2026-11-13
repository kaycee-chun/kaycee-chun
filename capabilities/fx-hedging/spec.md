---
title: Subsea Inspection Services FX Transaction Hedge Model, Technical Specification
scenario: subsea-inspection
llm_used: Claude (drafted from the course template)
---

**University of Hawaiʻi at Mānoa · Shidler College of Business**  
**FIN 321: International Finance and Securities**  
FX Transaction Hedging Project — Technical Specification

---

# Subsea Inspection Services Firm — FX Transaction Hedge Model · Technical Specification

> **Technical specification** for the EUR receivable hedge model: the named-range contract, calculation flow, and validation checks, precise enough that an AI or a colleague could rebuild the workbook from this document alone.

| Field | Value |
|------|------|
| **Created by** | Kaycee Chun |
| **Updated by** | Kaycee Chun |
| **Date Created** | 2026-11-06 |
| **Date Updated** | 2026-11-13 |
| **Version** | 1.1 |
| **LLM Used** (optional) | Claude, used to fill the template |
| **Role** | Treasury Analyst |
| **Audience** | CFO / Director of Treasury |
| **Companion Workbook** | student build |

---

## 1. Problem Statement

The U.S. subsea-inspection services firm has invoiced an Italian energy company EUR 8,600,000, to be received in 182 days. The rate is quoted in USD per EUR, with spot at 1.1140, so the receivable is worth $9,580,400 today. A 5% fall in the euro over the horizon would cut realized USD proceeds by about $479,000, and a 10% fall by about $958,000, and that loss comes straight out of cash flow. The objective is to protect USD value at settlement at an acceptable cost. This specification documents the framework used to quantify and compare four strategies (**no hedge**, **forward hedge**, **money-market hedge**, and **option (put) hedge**) and to produce the sensitivity evidence behind the Stage 5 decision memo. A payable variant (§4A) is also specified, using the EUR call.

---

## 2. Inputs (Known Variables)

All inputs are exposed as workbook **named ranges**, so the Calculation Flow (§4) reads the same whether implemented in Excel, Python, or an AI prompt. Dates, sources, and access timestamps are recorded in the Notes tab (see §2.3 for the Stage 4 source register). Market inputs (spot, forward, rates, premiums) are the only cells to adjust for scenario work.

### 2.1 Core Inputs

| Standardized Name | Description | Unit | Value |
|-------------------|-------------|------|------:|
| `FC_AMT` | Foreign-currency notional (receivable) | EUR | 8,600,000 |
| `S0_in` | Spot exchange rate at inception (placeholder: late-Oct 2026 market quote) | USD per EUR | 1.1140 |
| `F0_in` | Forward rate to settlement (indicative, per scenario) | USD per EUR | 1.1705 |
| `R_USD` | USD interest rate to settlement (placeholder: Fed range 3.75–4.00%) | Annual % | 3.90% |
| `R_FC` | EUR interest rate to settlement (placeholder: ECB deposit rate) | Annual % | 2.50% |
| `T_DAYS` | Days to settlement | Days | 182 |
| `BASIS` | Day-count denominator (USD and EUR both ACT/360) | Days | 360 |
| `K_PUT` | Put option strike (at spot, per scenario) | USD per EUR | 1.1140 |
| `PREM_PUT` | Put premium, USD per 1 EUR ($166,840 ÷ 8,600,000) | USD | 0.0194 |
| `K_CALL` | Call option strike (at spot, per scenario); used in the payable variant only | USD per EUR | 1.1140 |
| `PREM_CALL` | Call premium, USD per 1 EUR (per scenario); used in the payable variant only | USD | 0.0296 |

The EUR call protects a payable, so it does not enter the receivable calculations in §4. It is used in the payable variant in §4A.

### 2.2 Derived / Intermediate Values

| Name | Description | Formula |
|------|-------------|---------|
| `DF_USD` | USD accumulation factor | `1 + R_USD × T_DAYS / BASIS` = 1.01972 |
| `DF_FC` | EUR accumulation factor | `1 + R_FC × T_DAYS / BASIS` = 1.01264 |
| `FV_PREM_PUT` | Future value of put premium at settlement | `−PREM_PUT × FC_AMT × DF_USD` ≈ −$170,130 |
| `FV_PREM_CALL` | Future value of call premium at settlement (payable variant) | `−PREM_CALL × FC_AMT × DF_USD` ≈ −$259,579 |
| `S_T_grid` | Sensitivity spot grid at settlement | `S0_in` ± 5% in 1% steps (11 rows) |
| `USD_NO_HEDGE` | USD proceeds under no hedge | `S_T × FC_AMT` |

### 2.3 Stage 4 Source Register

Stage 4 repopulates the market inputs. Every input has a source row. The Notes tab reproduces this table with the access date for each. **Placeholders in square brackets are to be completed by the analyst before Stage 4 is signed off; none has been filled in by the drafter.** Current values stay as shown in §2.1 until refreshed values are supplied.

| Input | Source type | Source (publisher and page) | Access date |
|-------|-------------|-----------------------------|-------------|
| `S0_in` | Market data | [publisher and page to be recorded] | [YYYY-MM-DD] |
| `F0_in` | Market data / scenario quote | [publisher and page to be recorded] | [YYYY-MM-DD] |
| `R_USD` | Market data | [publisher and page to be recorded] | [YYYY-MM-DD] |
| `R_FC` | Market data | [publisher and page to be recorded] | [YYYY-MM-DD] |
| `PREM_PUT` | Scenario / option quote | [publisher and page to be recorded] | [YYYY-MM-DD] |
| `PREM_CALL` | Scenario / option quote | [publisher and page to be recorded] | [YYYY-MM-DD] |
| `FC_AMT` | Scenario term | Course scenario sheet | n/a |
| `T_DAYS` | Scenario term | Course scenario sheet | n/a |
| `BASIS` | Convention | Course scenario sheet (ACT/360, USD and EUR) | n/a |
| `K_PUT` | Scenario term | Course scenario sheet | n/a |
| `K_CALL` | Scenario term | Course scenario sheet | n/a |

---

## 3. Assumptions & Constraints

- **Quote convention:** All rates are **USD per euro**. A higher quote means the euro has appreciated, which is good for a euro receivable.
- **Horizon:** Single maturity, `T_DAYS = 182`.
- **Day-count basis:** USD and EUR money-market quotes are both ACT/360, so one `BASIS = 360` named range is correct for both legs here. (The rigorous `BASIS_USD` / `BASIS_FC` split only matters for GBP.)
- **Interest convention:** Simple interest over the period.
- **Parity:** The money-market hedge should replicate the forward under covered interest parity. Any gap is a test of parity in the quoted inputs, not a model error. Here the quoted forward (1.1705) sits about 4.9 cents above the parity forward (about 1.1218), so a gap is expected and is itself a finding.
- **Option premium:** Paid upfront in USD, quoted per 1 EUR (no contract multiplier), treated as a negative cash flow at t₀ and carried forward at `R_USD`. This applies to both the put and the call.
- **Counterparty / credit risk:** Excluded. All derivatives are assumed frictionless and creditworthy.
- **Transaction costs & bid-ask spreads:** Excluded from the base case; flagged as a sensitivity candidate.
- **Tax / accounting treatment:** Excluded. Pre-tax cash outcomes only.
- **Scenario construction:** `S_T` is varied deterministically across a grid, with no probability weights and no implied-volatility distribution.

### 3.1 Workbook Rules

These two rules apply to every tab and are part of the auditability checklist (§6.4).

1. **No cell shows an error.** No `#DIV/0!`, `#N/A`, `#VALUE!`, `#REF!`, `#NAME?`, or `#NUM!` appears anywhere in the workbook. Errors are prevented at the source (valid inputs, correct references, nonzero denominators) and are not hidden by wrapping formulas in `IFERROR` or similar.
2. **Every output is a formula.** Every output cell, including the strategy summary, sensitivity table, hedge-profit columns, winner labels, and chart data, is a formula that traces back to the named-range inputs. No output is a typed or pasted value. Only the input cells in §2.1 hold hardcoded numbers.

---

## 4. Calculation Flow

Named-range pseudocode, portable across Excel, Python, and AI prompts. Formulas in this section are for a **receivable**. The payable variant is in §4A.

### Step 1 — Derived inputs

1. `DF_USD` = `1 + R_USD × T_DAYS / BASIS`
2. `DF_FC` = `1 + R_FC × T_DAYS / BASIS`
3. `FV_PREM_PUT` = `−PREM_PUT × FC_AMT × DF_USD`

### Step 2 — Forward hedge (certainty benchmark)

- `USD_FWD` = `FC_AMT × F0_in` = $10,066,300
- Locked in at t₀; constant across the `S_T` grid.

### Step 3 — Money-market hedge (parity check)

1. Borrow `FC_AMT / DF_FC` euros today (the present value of the receivable), about EUR 8,492,665.
2. Convert to USD at spot: `(FC_AMT / DF_FC) × S0_in`, about $9,460,829.
3. Invest the USD to maturity: `USD_MM` = `(FC_AMT / DF_FC) × S0_in × DF_USD` ≈ $9,647,365.
4. At maturity the euro receivable repays the euro loan exactly, so the USD deposit is the locked-in proceed.

> **Parity check:** `USD_MM` implies a parity forward of `USD_MM / FC_AMT` ≈ 1.1218. The quoted forward of 1.1705 beats this by about $419,000. This is the gap the Stage 5 memo has to explain.

### Step 4 — Option hedge (floor with upside)

Put-and-hold on the receivable. Pay `PREM_PUT × FC_AMT` = $166,840 today for a put on EUR with strike `K_PUT`. At settlement, for each `S_T` on the grid:

- `USD_PUT(S_T)` = `MAX(S_T, K_PUT) × FC_AMT + FV_PREM_PUT`

### Step 5 — Sensitivity table (one row per grid point)

| Column | Output | Formula |
|--------|--------|---------|
| No hedge | `USD_NO_HEDGE(S_T)` | `S_T × FC_AMT` |
| Forward | `USD_FWD` | constant across rows |
| Money market | `USD_MM` | constant across rows |
| Option (put) | `USD_PUT(S_T)` | `MAX(S_T, K_PUT) × FC_AMT + FV_PREM_PUT` |
| Hedge profit | `HEDGE_PROFIT_k(S_T)` | `USD_k − USD_NO_HEDGE`, one sub-column per strategy |
| Overall winner (incl. no hedge) | label | `ARGMAX(USD_NO_HEDGE, USD_FWD, USD_MM, USD_PUT)` |
| Best active hedge (excl. no hedge) | label | `ARGMAX(USD_FWD, USD_MM, USD_PUT)` |

### Step 6 — Summary metrics

- `USD_FLOOR_PUT` = `MIN(USD_PUT)` across `S_T_grid` (the minimum the put guarantees, net of premium) = `K_PUT × FC_AMT + FV_PREM_PUT` ≈ $9,410,270 (net floor rate about 1.0942).
- `S_BREAKEVEN_FWD_NOHEDGE` = `F0_in` = 1.1705, the settlement rate above which no hedge beats the forward (about 5.1% above spot, just outside the ±5% grid).
- `S_BREAKEVEN_PUT_FWD` = `F0_in − FV_PREM_PUT / FC_AMT` ≈ 1.1903, the settlement rate above which the put outperforms the forward (about 6.8% above spot, outside the grid).
- `USD_BASE_k` = `USD_k` evaluated at `S_T = S0_in` for each strategy (feeds §5.1).

---

## 4A. Payable Variant (EUR call)

For a EUR **payable** of `FC_AMT` due at `T_DAYS`, the exposure reverses: a *higher* `S_T` raises the USD cost. Results below are **USD cost** (lower is better). Inputs are the same named ranges, plus `K_CALL` and `PREM_CALL`.

1. `FV_PREM_CALL` = `−PREM_CALL × FC_AMT × DF_USD` (a negative cash flow at settlement; cost = `−FV_PREM_CALL`)
2. **No hedge:** `COST_NO_HEDGE(S_T)` = `S_T × FC_AMT`
3. **Forward (buy EUR forward):** `COST_FWD` = `FC_AMT × F0_in`, constant across the grid.
4. **Money market:** Buy `FC_AMT / DF_FC` euros today at spot, invest at `R_FC`; at maturity the deposit pays the payable. `COST_MM` = `(FC_AMT / DF_FC) × S0_in × DF_USD`.
5. **Option (call):** Pay `PREM_CALL × FC_AMT` today for a call on EUR with strike `K_CALL`. At settlement:
   - `COST_CALL(S_T)` = `MIN(S_T, K_CALL) × FC_AMT − FV_PREM_CALL`
   - `COST_CAP_CALL` = `MAX(COST_CALL)` across the grid = `K_CALL × FC_AMT − FV_PREM_CALL` (the most the call allows you to pay, premium included).
6. **Call payoff** (before premium) at settlement: `MAX(S_T − K_CALL, 0) × FC_AMT`.
7. **Hedge benefit** per strategy: `COST_NO_HEDGE − COST_k`. Best strategy per row: `ARGMIN` of the four costs.
8. **Kink check:** at `S_T = K_CALL`, `COST_CALL` equals `K_CALL × FC_AMT − FV_PREM_CALL`.

The payable variant uses the same grid, tabs, and workbook rules (§3.1) as the receivable model.

---

## 5. Outputs

| Output | Description | Format | Purpose |
|--------|-------------|--------|---------|
| Input panel | All named-range inputs with units, sources, and access dates | Top of each tab | Single source of truth |
| Strategy summary | `USD_FWD`, `USD_MM`, `USD_BASE_k`, and `USD_FLOOR_PUT` | Table above the grid | Executive at-a-glance |
| Sensitivity table | USD proceeds for each strategy across `S_T_grid` ± 5% | Table | Core analytical evidence |
| Hedge-profit columns | `USD_k − USD_NO_HEDGE` per strategy per row | Sub-table | Isolates hedge value-add |
| Winner / best-hedge labels | `ARGMAX` labels per row | Two label columns | Quick-read decision cue |
| Sensitivity chart | Line chart of USD proceeds vs. `S_T`, all four strategies | Embedded chart | Visual comparison |
| Executive summary (Stage 5 memo) | 1–2 paragraph narrative with explicit recommendation | Separate memo | Downstream deliverable |

Every output in this table is a formula (§3.1), except the Stage 5 memo, which is a separate written document.

### 5.1 Computed Base-Case Values

Hand-computed at `S_T = S0_in = 1.1140`. These are the regression checkpoint for the Stage 3 build.

| Strategy | USD Proceeds (Receivable) | Hedge Profit vs. No Hedge |
|----------|--------------------------:|--------------------------:|
| No hedge | $9,580,400 | — |
| Forward | $10,066,300 | $485,900 |
| Money market | ≈ $9,647,365 | ≈ $66,965 |
| Option (put) | ≈ $9,410,270 (equals the put floor, because spot = strike) | ≈ −$170,130 |

Expected sensitivity grid (USD proceeds; forward and money market are constant at $10,066,300 and $9,647,365):

| `S_T` | Move | No hedge | Put | Overall winner |
|------:|-----:|---------:|----:|----------------|
| 1.0583 | −5% | $9,101,380 | $9,410,270 | Forward |
| 1.0694 | −4% | $9,197,184 | $9,410,270 | Forward |
| 1.0806 | −3% | $9,292,988 | $9,410,270 | Forward |
| 1.0917 | −2% | $9,388,792 | $9,410,270 | Forward |
| 1.1029 | −1% | $9,484,596 | $9,410,270 | Forward |
| 1.1140 | 0% | $9,580,400 | $9,410,270 | Forward |
| 1.1251 | +1% | $9,676,204 | $9,506,074 | Forward |
| 1.1363 | +2% | $9,772,008 | $9,601,878 | Forward |
| 1.1474 | +3% | $9,867,812 | $9,697,682 | Forward |
| 1.1586 | +4% | $9,963,616 | $9,793,486 | Forward |
| 1.1697 | +5% | $10,059,420 | $9,889,290 | Forward |

---

## 5A. Tab Architecture

The workbook has nine tabs, in this order. Each tab's purpose is fixed; nothing is calculated on a tab other than the one named.

| # | Tab | Purpose |
|---|-----|---------|
| 1 | **Cover** | Title, author, date, version, audience, and the list of data sources. |
| 2 | **Legend** | Cell color and format conventions: yellow inputs, blue assumptions, black formulas, green cross-tab links. Also the sign convention and units. |
| 3 | **Inputs** | The §2.1 named-range inputs with units. The only tab with hardcoded numbers. Derived values from §2.2 sit directly below. |
| 4 | **No Hedge** | USD proceeds (receivable) or cost (payable) of leaving the exposure open, across the `S_T` grid. |
| 5 | **Forward** | The forward hedge calculation (§4 Step 2, §4A item 3). |
| 6 | **Money Market** | The money-market hedge, step by step, and the parity check against the forward (§4 Step 3, §4A item 4). |
| 7 | **Option** | The put (receivable) and call (payable) hedges, premium carry, payoff, floor or cap, and kink check (§4 Step 4, §4A item 5). |
| 8 | **Sensitivity** | The sensitivity table, hedge-profit columns, winner labels, summary metrics, and the four-strategy chart (§4 Steps 5–6, §7). |
| 9 | **Notes** | The Stage 4 source register with access dates (§2.3), assumptions, known limitations, change log, and the §6.3 independent-check results. |

All tabs follow the workbook rules in §3.1.

---

## 6. Model Review — What Worked & What to Improve

This spec is written before the build, so §6 is a known-risks register.

### 6.1 What Worked

- The forward ($10,066,300) and no-hedge ($9,580,400) figures follow directly from `FC_AMT`, `F0_in`, and `S0_in` and reproduce by hand.
- The money-market hedge reproduces from the 3.90% and 2.50% rates at ACT/360, and its implied forward (1.1218) matches `S0_in × DF_USD / DF_FC` computed directly, which supports the day-count choice.
- The put kink is checkable: at `S_T = K_PUT` the payoff equals `K_PUT × FC_AMT + FV_PREM_PUT` = $9,410,270.

### 6.2 What to Improve

- **Parity gap:** the quoted forward is about $419,000 above the money-market result. The build must show this gap explicitly rather than force `USD_FWD = USD_MM`, and the memo must say whether the quote is realistic or the inputs need re-checking. The quote would be consistent with a spot near 1.162, which suggests it is stale against today's spot.
- **Grid width:** the ±5% grid stops at 1.1697, just short of the forward breakeven (1.1705). Make the grid step and width adjustable and consider ±10%.
- **Premium convention:** the model carries the premium forward at `R_USD` (floor 1.0942); the Stage 1 brief used the undiscounted premium (1.0946). Label both in the workbook.
- **Named ranges:** every formula uses the §2.1 names, with no bare cell references.
- **Chart:** build the four-strategy line chart with the put's kink visible at `K_PUT`.
- **Dated sources:** record the source and access date for every input in the Notes tab (§2.3).

### 6.3 Independent Check (before the Stage 5 memo)

Before the memo is written, the model is checked in two ways:

1. **Hand recomputation:** I recompute `USD_FWD`, `USD_MM`, `USD_PUT` at the strike, and `FV_PREM_PUT` in a separate sheet with plain formulas, and compare them to the model outputs. Any difference over 0.05% is traced to its cause before proceeding.
2. **Outside reference:** I compare the parity forward (about 1.1218) with a published market forward for the same tenor, and ask a second reader (a classmate or a fresh AI session) to rebuild the base case from this spec alone. If their numbers do not match §5.1, the spec is unclear and gets fixed.

### 6.4 Auditability Checklist

- [ ] Every input has a standardized named range from §2.1
- [ ] Every formula in §4 and §4A uses named ranges, with no bare `$F$n` references
- [ ] Money-market hedge compared with forward hedge and the gap reported (parity check)
- [ ] Put payoff at `S_T = K_PUT` equals `K_PUT × FC_AMT + FV_PREM_PUT` (kink verification)
- [ ] Call cost at `S_T = K_CALL` equals `K_CALL × FC_AMT − FV_PREM_CALL` (payable kink verification)
- [ ] Sensitivity grid is symmetric around `S_T = S0_in`
- [ ] Notes tab records a source and access date for every input (§2.3)
- [ ] No cell in the workbook shows an error (§3.1, rule 1)
- [ ] Every output is a formula, with no typed or pasted values (§3.1, rule 2)
- [ ] The workbook has the nine tabs in §5A, in that order
- [ ] Cell colors match the legend: yellow inputs, blue assumptions, black formulas, green cross-tab links
- [ ] Independent check in §6.3 completed and results logged

---

## 7. Sensitivity Plan

- **Grid:** `S_T_grid` spans `S0_in × (1 ± 5%)` in 1% increments, giving 11 rows including the baseline.
- **Strategies plotted:** no hedge, forward, money market, put option.
- **Primary chart:** `S_T` on the x-axis, USD proceeds on the y-axis. Forward and money-market lines are horizontal, no-hedge is a straight line, and the put is piecewise-linear with a kink at `K_PUT`.
- **Secondary table:** hedge profit vs. no hedge for each strategy.
- **What the chart should communicate:** the trade-off between **certainty** (forward, money market), **optionality** (put), and **naked exposure** (no hedge).

---

## 8. Limitations & Next Steps

**Limitations.** This specification does not incorporate:
- Partial, layered, or dynamic hedging (static, full-notional hedge at t₀)
- Credit, counterparty, and settlement risk
- Implied-volatility-based option pricing (the premiums are scenario inputs)
- Hedge accounting (ASC 815 / IFRS 9)
- Multi-currency or multi-horizon portfolio effects

It also relies on placeholder spot and rates that are about a week old, and on an indicative forward that is inconsistent with them.

**Next steps.** Stage 3 builds the workbook from §4, §4A, and §5A and checks it against §5.1. Stage 4 repopulates it with refreshed market inputs and completes the source register in §2.3. Stage 5 runs the independent check in §6.3 before the decision memo, then writes the final recommendation to the CFO.

---

## Appendix A — Change Log

| Version | Date | Author | Change |
|---------|------|--------|--------|
| 0.1 | 2026-11-06 | Kaycee Chun | Initial draft |
| 1.1 | 2026-11-13 | Kaycee Chun | Added tab architecture (§5A); added `K_CALL` and `PREM_CALL` to inputs and the payable variant (§4A); added Stage 4 source register (§2.3); added workbook rules (§3.1): no cell shows an error, every output is a formula |
