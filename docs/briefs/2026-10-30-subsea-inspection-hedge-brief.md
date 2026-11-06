---
type: brief
title: "EUR 8.6M Receivable Hedge Brief: Subsea Inspection"
scenario: "subsea-inspection"
date: 2026-10-30
hypothesis: "I expect the quoted 1.1705 forward to lock in far more USD (~$10.07M) than a money-market hedge (~$9.65M) because it embeds a ~5% euro premium that a ~1.4-point rate gap cannot justify, so the quote is likely stale versus today's ~1.114 spot."
llm_used: "Claude"
---

# EUR 8.6M Receivable Hedge Brief: Subsea Inspection

**Created by:** Kaycee Chun   
**Updated by:** Kaycee Chun  
**Date Created:** 2026-10-30  
**Date Updated:** 2026-10-30  
**Version:** 1.0 
**LLM Used:** Claude

---

## Executive Summary 

Our firm will receive EUR 8,600,000 from an Italian energy client in 182 days. The euro amount is fixed, but its dollar value is not. At a spot near 1.114 the receivable is worth about $9.58 million, and a 10% euro decline would remove roughly $958,000 from budgeted cash. This brief frames that exposure and compares three hedge families: a forward (indicatively quoted at 1.1705), a money-market hedge, and a EUR put option (premium $0.0194 per EUR, about $166,840). I expect the quoted forward to look too good relative to today's spot and interest rates, so I will test it before recommending anything. The recommendation waits for Stage 5.

---

## Background & Objectives

**Exposure.** Currency EUR, amount 8,600,000, settlement in 182 days. We are long euros, so a weaker euro hurts us.

| EURUSD at settlement | USD proceeds | vs. today (1.114) |
|---|---|---|
| 1.1140 (unchanged) | $9,580,400 | n/a |
| 1.0583 (-5%) | $9,101,400 | -$479,000 |
| 1.0026 (-10%) | $8,622,400 | -$958,000 |

**Three hedge families.**

| Family | Mechanics | Pro | Con |
|---|---|---|---|
| Forward | Sell EUR 8.6M at a locked rate in 182 days | Full certainty; budget is safe | Gives up all upside if EUR rises |
| Money-market | Borrow EUR today, convert at spot, invest the USD | Same certainty as a forward, built from rates | Uses borrowing capacity; more moving parts |
| Put option (strike at spot) | Right, not obligation, to sell EUR at 1.114 | Floor plus upside participation | Premium of about $166,840 (1.7% of notional) paid up front |

The forward is like selling the house at a fixed price; the put is like fire insurance: you pay a premium and hope to waste it. The EUR call (premium $0.0296) protects a EUR payable, so it is not a candidate here.

**Objective:** protect the USD value of the receivable. Secondary: understand what certainty costs.

---

## Methods

I will build a workbook comparing USD proceeds across settlement rates for each hedge, then validate it independently.

**What I am assuming.** Spot is about 1.1140 (late-October market quotes). USD rate about 3.90% (Fed range 3.75-4.00%) and EUR rate about 2.50% (ECB deposit rate), both placeholders. Simple interest, 182/360 day count. No counterparty, margin, or tax effects. Option premium is not financed.

**Hypothesis.** I expect the forward to lock in about $10.07M (8.6M x 1.1705) and the money-market hedge about $9.65M (implied forward about 1.1218), because the quoted forward embeds a +5.1% premium over spot, while the rate gap justifies only about +0.7%. The put floor is about 1.0946 net of premium, or about $9.41M.

**How I would know I was wrong.** If refreshed spot and rates put the money-market hedge within about 0.2% of the 1.1705 forward (this would need spot near 1.16), or a bank firmly quotes 1.1705 against today's spot, the mismatch is a data problem, not mispricing.

---

## Limitations & Next Steps

**Limitations.** The 1.1705 forward is indicative and appears inconsistent with today's spot. Sources disagree on the ECB rate (2.50% vs. 2.65%), and no rates are sourced from a bank or official fixing yet. Spot is a single-day figure.

**Next steps (owner: kaycee chun):**

1. **Stage 2, model specification:** workbook designed on paper (named ranges, tabs, formulas, checks)
2. **Stage 3, AI-assisted build and audit:** AI generates the workbook; I audit and fix it
3. **Stage 4, live market data:** sourced, timestamped spot, rates, and forward replace placeholders
4. **Stage 5, validation and decision memo:** independent LLM run, hand verification, and the final recommendation

---

## References

Michalowski, G. (2026, October 28). *The full statement from the October 2026 FOMC rate decision*. investingLive. https://investinglive.com/central-banks/the-full-statement-from-the-october-2026-fomc-rate-decision/

RoboForex. (2026, October 29). *EURUSD forecast and analysis for today, 29 October 2026*. https://roboforex.com/beginners/analytics/forex-forecast/currencies/eur-usd-forecast-2026-10-29/

RoboForex. (2026, October 30). *EURUSD forecast and analysis for today, 30 October 2026*. https://roboforex.com/beginners/analytics/forex-forecast/currencies/eur-usd-forecast-2026-10-30/

RoboForex. (2026, October 13). *EURUSD forecast and analysis for today, 13 October 2026*. https://roboforex.com/beginners/analytics/forex-forecast/currencies/eur-usd-forecast-2026-10-13/

Stauffer, A. (2026). *Stage 1: Executive brief*. Kumu, Finance & Securities. https://adamwstauffer.github.io/ai-lms/fx-hedging-stage1.html
