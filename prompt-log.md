# Prompt Log

## 2026-10-09
- **What I asked:** Requested initial setup for portfolio repository `firstname-lastname` with standard directory skeleton, baseline `AGENTS.md` tailored to my resume, `CLAUDE.md`, `.gitignore`, `RESUME.md`, and first log entry.
- **What you produced:** Generated full repository layout, `.gitignore`, canonical rule pointers, tailored `AGENTS.md`, `RESUME.md`, and initial `prompt-log.md` entry.
- **What was wrong and how it was caught:** N/A (initial repository setup session created directly from specification).

## 2026-11-13: Stage 3 build (subsea-inspection)

### Prompt 1
> https://github.com/kaycee-chun/kaycee-chun/blob/main/capabilities/fx-hedging/spec.md
> Here is my model specification. Build the workbook it specifies. Every requirement in the spec's build contract is binding: all ten named ranges, formulas only (no pasted values), cover page, Legend/Key tab with the color convention, all three hedge families, the ±5% sensitivity table with chart, and the validation checks computed in the workbook. Give me the result as a downloadable .xlsx file — not a table in the chat, not a .csv, and not a Word or PDF document. If anything in the spec is ambiguous, ask me questions before you build.

### Prompt 2
> can you read this

(Attached: the text of `spec.md` v1.1.)

### Prompt 3
> go ahead and build it with these defaults
