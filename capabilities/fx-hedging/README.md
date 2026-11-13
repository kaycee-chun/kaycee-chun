# FX Transaction Hedging

FIN 321: International Finance and Securities, Shidler College of Business, University of Hawaiʻi at Mānoa.

This folder holds the technical specification for an FX transaction hedge model. The scenario is **subsea-inspection**: a U.S. subsea-inspection services firm is owed EUR 8,600,000 by an Italian energy company, due in 182 days. The model compares four ways to protect the USD value at settlement: no hedge, a forward, a money-market hedge, and a put option. A payable variant using a EUR call is also specified.

## Contents

| File | Description |
|------|-------------|
| [`spec.md`](capabilities/fx-hedging/spec.md) | Technical specification: inputs, calculation flow, payable variant, tab architecture, outputs, and validation checks. Detailed enough to rebuild the workbook from the document alone. |
| `README.md` | This file. |

The Excel workbook is a separate student build and is not stored here.

## Workbook Layout

| Tab | Purpose |
|-----|---------|
| Cover | Author, date, version, and data sources |
| Legend | Color and format conventions |
| Inputs | Named-range inputs and derived values; the only tab with hardcoded numbers |
| No Hedge | Open-exposure results across the settlement-rate grid |
| Forward | Forward hedge |
| Money Market | Money-market hedge and parity check against the forward |
| Option | Put (receivable) and call (payable) hedges |
| Sensitivity | Sensitivity table, hedge profit, winner labels, and chart |
| Notes | Source register with access dates, assumptions, limitations, change log |

## Workbook Rules

- No cell shows an error.
- Every output is a formula; only the input cells hold typed numbers.

## Project Stages

| Stage | Work |
|-------|------|
| 3 | Build the workbook from `spec.md` and check it against the base-case values in §5.1 |
| 4 | Refresh market inputs and complete the source register (§2.3) |
| 5 | Independent check (§6.3), then the decision memo to the CFO |

## Status

Version 1.1, updated 2026-11-13. The Stage 4 source register in §2.3 still has placeholders for the publisher, page, and access date of each market input.

## Author

Kaycee Chun, Treasury Analyst. Audience: CFO / Director of Treasury.
