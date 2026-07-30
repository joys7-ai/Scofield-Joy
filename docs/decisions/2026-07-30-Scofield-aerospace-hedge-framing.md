---
title: FX Receivable Exposure — Aerospace Export Contract
to: Chief Financial Officer
from: Joy Scofield
date: 2026-07-30
re: $20,000,000 receivable — hedging framing and recommended next steps
---

## Executive Summary

Our firm has a $20,000,000 receivable settling in 1 year from an aerospace 
export contract, denominated based on a EURUSD exchange rate. At today's 
indicative forward rate (EURUSD 1.0935), the euro amount behind that dollar 
figure is fixed, but exchange-rate moves between now and settlement change 
what we actually collect. A move of just a few cents in EURUSD can swing 
proceeds by hundreds of thousands of dollars. I recommend we hedge, and I've 
outlined three ways to do it below. I'd like approval to move into Stage 2: 
building the model that will let us pick the right one with real data.

## Background

As a U.S. aerospace manufacturer, this contract's value is tied to EURUSD. 
Between now and settlement in 1 year, the rate can move meaningfully — a 
normal year's volatility is well within the range that would matter here. 
Because our costs are dollar-denominated, currency risk sits entirely on 
the revenue side: we don't control the exchange rate, but it controls our margin.

## Findings — Three Hedge Families

**Forward contract:** Lock in the 1.0935 rate today for delivery in 1 year. 
Removes the risk entirely — budget certainty. Trade-off: we give up any 
upside if the euro strengthens, and it ties up counterparty/margin capacity.

**Money-market hedge:** Borrow EUR now, convert to USD at spot, invest the 
proceeds. Achieves the same certainty as a forward synthetically, useful if 
forward pricing looks unfavorable. Trade-off: consumes borrowing capacity 
and adds moving parts to manage.

**Options (put floor):** Buy the right, not the obligation, to sell EUR at 
a strike near today's spot. Protects the downside while keeping upside if 
the euro rallies. Trade-off: costs a premium upfront (quoted around $0.019 
per contract for the put), hedge or not — it's insurance, not a lock.

## Implications

Given the size of this exposure, doing nothing is itself a decision to 
speculate on FX. I lean toward a forward or option depending on how much 
upside we're willing to trade for certainty — that tradeoff is exactly what 
Stage 2's model will let us quantify rather than guess at.

## Limitations & Next Steps

This memo is a framing document — no live pricing yet; spot rate, strike, 
and interest rates are still placeholders to be set at market close on the 
day Stage 2 begins. Approving this framing moves us to:
- **Stage 2:** Build the model spec (named ranges, formula logic, validation checks)
- **Stage 3:** AI-assisted build, which I'll audit line by line
- **Stage 4:** Replace placeholders with live, sourced, timestamped market data
- **Stage 5:** Independent validation and a final hedge recommendation

I'll bring the finished analysis back for your sign-off before we execute anything.

## References

- Course scenario assignment, FIN 321, Scofield — #20, Aerospace Manufacturer, $20,000,000 receivable, 1-year maturity
- Indicative forward rate: EURUSD 1.0935 (course-provided)
