# Prompt Log

## 2026-10-09
- **What I asked:** Requested initial setup for portfolio repository `firstname-lastname` with standard directory skeleton, baseline `AGENTS.md` tailored to my resume, `CLAUDE.md`, `.gitignore`, `RESUME.md`, and first log entry.
- **What you produced:** Generated full repository layout, `.gitignore`, canonical rule pointers, tailored `AGENTS.md`, `RESUME.md`, and initial `prompt-log.md` entry.
- **What was wrong and how it was caught:** N/A (initial repository setup session created directly from specification).

## 2026-11-13

- **What I asked:** Asked Claude to build the FX hedge workbook from my committed spec (GitHub link), with all named ranges, formulas only, a cover page, a Legend/Key tab, all three hedge families, a ±5% sensitivity table with chart, and live validation checks, delivered as a downloadable .xlsx. Told it to ask me questions first if anything in the spec was ambiguous.
- **What you produced:** No workbook yet. Claude could not open the GitHub link, so it asked five questions: which spec was binding, which hedge families, receivable only or payable too, which input values, and which validation checks.
- **What was wrong and how it was caught:** My prompt said "ten named ranges" and a "build contract," but the spec has 11 named inputs and no section by that name. Claude caught it by comparing the prompt with the spec text and asked before building.
##
- **What I asked:** Pasted the full v1.1 spec into the chat and asked whether Claude could read it.
- **What you produced:** Confirmed it could read the spec, repeated the two mismatches (11 names, no build contract section), and proposed six defaults: all names, forward/money market/option plus no hedge, receivable plus the payable variant, spec §2.1 values unchanged, six validation checks, and the nine-tab layout.
- **What was wrong and how it was caught:** N/A (clarification step; the open points were already on the table).
##
- **What I asked:** Approved the six defaults and told Claude to build the workbook.
- **What you produced:** `FX_Hedge_Model_Subsea_Inspection.xlsx` with nine tabs, 50 named ranges, 652 formulas, sensitivity tables and charts for the receivable and the payable variant, and validation checks on the Notes tab. It recalculated with no errors and every check passed.
- **What was wrong and how it was caught:** Two validation checks first failed by $3.38 because the spec's "≈" money-market figure ($9,647,365) uses rounded intermediates and the workbook computes $9,647,361.62. The checks caught it; I applied the spec's own 0.05% tolerance to the "≈" values. Later, the Stage 3 page showed its color contract (green = formulas, gray = outputs), which differs from the workbook's colors. That is not fixed yet.
