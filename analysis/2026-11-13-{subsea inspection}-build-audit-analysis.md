---
title: Subsea Inspection FX Hedge Workbook, Build Audit
scenario: subsea-inspection
date: 2026-11-13
workbook: FX_Hedge_Model_Subsea_Inspection.xlsx
spec: capabilities/fx-hedging/spec.md (v1.1)
auditor: Claude (build and audit); owner Kaycee Chun
---

# Build audit: subsea-inspection FX hedge workbook

## 1. How this audit was done

**Excel was not opened.** The audit ran in a sandbox without Excel, so each Excel step was replaced by an equivalent test on the saved file. Anything below that depends on Excel's own behavior (Name Manager display, chart rendering, recalculation on open) is **not verified** and is listed in §5.

| Requested step | What was done instead |
|---|---|
| Name Manager | Read the 50 defined names from the saved file with openpyxl and checked each resolves to a real sheet |
| Formulas vs typed numbers | Counted formulas and typed numeric constants on every tab from the saved file |
| Change `S0_in` | Made copies with `S0_in` set to 1.2000 and 1.0000, recalculated each in LibreOffice, compared the outputs |
| §7 checks | Checked the grid, series, chart definitions, and tables against spec §7; viewed a LibreOffice rendering of the Sensitivity tab |

## 2. Results against the four questions

| Question | Result | Evidence |
|---|---|---|
| Do the named ranges exist? | **Yes** (in the file; Name Manager itself not viewed) | 50 names, 0 unresolved. All 11 inputs from spec §2.1, the 4 derived values from §2.2, and the output names from §4 and §4A are present |
| Are calculated cells formulas? | **Yes** | 652 formulas. Typed numbers appear only on Inputs (55). Cover, Legend, No Hedge, Forward, Money Market, Option, Sensitivity and Notes hold 0 typed numbers. Every output cell on Sensitivity is a formula |
| Does the sensitivity table move with `S0_in`? | **Yes** | See finding 4 |
| Do the §7 checks pass? | **Yes, with one caveat** | See §3 |

## 3. §7 sensitivity plan, item by item

| §7 requirement | Result |
|---|---|
| Grid spans `S0_in × (1 ± 5%)` in 1% steps, 11 rows including baseline | Pass: S_T runs 1.0583 to 1.1697, midpoint 1.1140 equals `S0_in` |
| Strategies plotted: no hedge, forward, money market, put | Pass: the receivable chart has 4 series, each tied to its Sensitivity column; a second chart does the same for the payable variant |
| Forward and money market horizontal, no hedge straight, put piecewise-linear with a kink at `K_PUT` | Pass in the data: forward and money-market columns are constant, and the put has a grid vertex exactly at 1.1140. The kink is visible in the LibreOffice rendering |
| Secondary table: hedge profit vs no hedge | Pass: three profit columns per row |
| Chart should show certainty vs optionality vs naked exposure | Partly: see finding 5 |

## 4. Findings

### Finding 1 (high): the put floor and call cap are wrong when the grid does not reach the strike

`USD_FLOOR_PUT` is defined as `MIN(USD_PUT)` over the grid, as in spec §4 Step 6, and `COST_CAP_CALL` as `MAX(COST_CALL)`. These equal the true floor and cap only if the grid reaches the strike. With `S0_in` = 1.2000 the grid starts at 1.1400, above `K_PUT` = 1.1140, so the workbook reports a floor of **$9,633,870** when the true floor is **$9,410,270**. The Cover headline and Sensitivity summary both show the wrong value. The reverse happens with `S0_in` = 1.0000 and the call cap. The validation checks caught both cases (put kink failed at 1.2, call kink failed at 1.0), so the error is flagged, but the headline numbers are still wrong until someone reads the Notes tab.

*Recommendation:* define the floor and cap by the closed form (`K_PUT × FC_AMT + FV_PREM_PUT`, `K_CALL × FC_AMT − FV_PREM_CALL`) and keep the grid minimum and maximum as the cross-check. This changes how spec §4 Step 6 is stated, so the spec needs the same edit. *Status: not changed.*

### Finding 2 (high): strikes and the forward do not follow spot

`K_PUT` and `K_CALL` are typed inputs, as spec §2.1 requires, even though the scenario says they sit "at spot". Changing `S0_in` alone leaves the options in or out of the money and leaves the quoted forward and premiums at their old values. At `S0_in` = 1.2000 the parity gap flips from +$418,938 to −$325,831, and the winner changes from Forward to Money market on most rows. The model recalculates correctly, but the result is a mix of new spot with old option terms and an old forward, which is not a coherent scenario. A user changing spot for scenario work will not be warned.

*Recommendation:* either add a note beside `S0_in` on the Inputs tab saying that `K_PUT`, `K_CALL`, `F0_in`, and both premiums must be refreshed together, or add a check that flags when a strike differs from spot while the source note says "at spot". *Status: not changed.*

### Finding 3 (medium): one overall status mixes regression checks with integrity checks

Checks 8 to 17 compare the workbook to the spec's hard-coded §5.1 base case. They pass today and will fail by design after Stage 4 loads refreshed market inputs. At `S0_in` = 1.2000 the overall status reads "FAILURES: 11", of which only checks 3, 4 and 20 point at a real issue (finding 1); the other eight are expected differences from the old base case. A reader seeing 11 failures after a normal refresh may distrust a sound model, or stop reading failures at all.

*Recommendation:* split the status into **Model integrity** (parity forward, kinks, symmetry, hand recomputation, no errors) and **Spec §5.1 regression** (reported as "not applicable after refresh" when inputs differ from the base case). *Status: not changed.*

### Finding 4 (info, positive): the sensitivity table is live

Setting `S0_in` to 1.2000 moved every spot-dependent cell. The grid became 1.1400 to 1.2600. No-hedge proceeds at the low, middle and high rows became $9,804,000, $10,320,000 and $10,836,000. The money-market result became $10,392,131 and the put values moved with the grid. The forward stayed at $10,066,300 because `F0_in` is a separate input, which is correct. The recalculation reported 0 errors in both scenario copies.

### Finding 5 (medium): the default ±5% grid never shows the crossover that matters

Spec §6.2 already notes that the forward breakeven (1.1705) lies just outside the grid. The consequence is that the "Overall winner" column reads "Forward" in all 11 rows, and the chart shows the forward line above no hedge everywhere, so it cannot show the trade-off §7 wants it to communicate. Widening `GRID_STEP` to 2% (±10%) puts the breakeven inside the grid: the winner becomes "No hedge" from +6% upward and the best active hedge becomes the put from +8%. The only failures at 2% were the three §5.1 grid comparisons, which is expected.

*Recommendation:* keep ±5% as the regression grid, but run the memo's chart at ±10%. *Status: the control exists (`GRID_STEP`); the default was not changed because the spec fixes ±5%.*

### Finding 6 (low): "≈" values in the spec differ from the workbook by $3.38

Spec §5.1 gives the money-market result as ≈ $9,647,365. The workbook computes $9,647,361.62 from the same formula, because the spec rounds intermediate steps (DF_USD to 1.01972). Two checks failed at first for this reason. I applied the §6.3 tolerance of 0.05% to the four values the spec marks "≈" (money market, put, and their hedge profits), and kept a $1 tolerance for values the spec states exactly. This is a judgment call, and the spec should either say "≈ $9,647,362" or state the unrounded figure.

## 5. Summary

The workbook is built as specified: names present, calculations are formulas, the table moves with spot, and the §7 structure is met. The two high findings mean headline floor and cap values can be wrong, and a scenario change can look coherent when it is not, after any change to `S0_in` or the grid. Both are fixable in the workbook and the spec in a few formulas. Nothing was changed in the workbook as part of this audit.
