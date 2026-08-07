# Stage 4 review — aerospace market data & population · Treasury sign-off

Joy — this memo does the thing Stage 4 is actually testing for: it closes a loop you opened two stages ago. You predicted in Stage 2 that the parity check would fail on course-assigned inputs, confirmed the diagnosis in the Stage 3 audit, and here you proved it by loading one internally consistent live dataset and watching the gap collapse from ~$1.5M to $231. That is a hypothesis carried across three deliverables and settled with evidence.

| Criterion | Score |
|---|---|
| Data quality & provenance | 50 / 50 |
| Model resolves cleanly | 32.6 / 33 |
| Lab cross-check | 17 / 17 |
| **Total** | **100 / 100** |

**What you did well — and why it matters**

- **Every input is re-pullable by a stranger.** Named vendor, dated timestamp, and a unit convention on each row. An auditor can reconstruct your entire input set six months from now without asking you a question — which is the whole point of provenance, and the part most people treat as paperwork.
- **You justified rate *choices*, not just rate values.** Moving `R_USD` from an overnight policy rate to the 1-Year Treasury constant maturity, and `R_FC` from the ECB deposit rate to 12M Euribor, because both need to match the model's 365-day horizon — that is tenor matching, and it is the single most common thing people get wrong when they first populate a carry model. You did it deliberately and said why.
- **You disclosed a stale input instead of hiding it.** Euribor 12M dated 2026-06-30 against an 2026-08-05 retrieval is about five weeks old, and you said so plainly in §6 rather than letting the date column imply freshness. Volunteering the weakest link in your own dataset is what makes the rest of it credible.
- **The lab cross-check is honest about scope.** Forward and money-market tie exactly. Rather than forcing an options comparison, you explained that the Lab uses a simple premium while your workbook future-values it per your own spec — two legitimate conventions, so the comparison would be meaningless. Declining to report a number is sometimes the correct result.

**To push it further (real-desk nuance)**

- **A CIP-implied forward is a model output, not a quote.** You flagged this, and the residual of −0.0000115 confirms the arithmetic. But be clear-eyed about what that residual proves: it proves your forward and your money-market leg were built from the same inputs. It does *not* validate against the market, because no market price entered the calculation. A real desk would still ask what the dealer would actually quote, and the gap to that quote is the cross-currency basis plus spread.
- **A five-week-stale EUR leg has a size.** Take the next step from disclosure to quantification: at a 365-day tenor, a 25bp move in `R_FC` shifts the CIP forward by roughly 0.0028 — about $56k on EUR 20M. Now the reader knows whether the staleness matters. "Stale, and here's the magnitude" is a materially stronger sentence than "stale."
- **The 6.9% spot gap deserves one more sentence.** You correctly attribute it to the scenario's forward being set against a different baseline spot. Worth stating the consequence explicitly: every Stage 2/3 dollar figure you produced is now superseded, and any conclusion drawn from that grid must be re-derived, not carried forward.

**Next — Stage 5**

You now have a workbook whose inputs are consistent and whose parity check passes, which means for the first time the strategy comparison is trustworthy. Stage 5 asks you to hand this to an LLM and then break its answer. Three things to hand-verify with arithmetic before you accept any recommendation: the forward proceeds, the put floor, and the crossover spot where the option overtakes the forward. And frame the recommendation on the CFO's risk tolerance — certainty versus asymmetric upside — not on whichever line happens to sit highest on your grid.

— Treasury

---

### How to work this review — professional workflow

Treat this PR the way an analyst treats feedback from Treasury — a review is a proposal to engage with, not a checklist to rubber-stamp:

1. **Read it yourself first.** Understand each point and form your own view before changing anything. Disagreeing *with a documented reason* is a legitimate, senior response.
2. **Stress-test it with an LLM (pushback pass).** Paste this review and your spec into your AI assistant and ask it to (a) explain anything you're unsure of more deeply, and (b) argue the *other side* — where might the reviewer be wrong, and what would you give up by making each change. You're building judgment, not just executing edits.
3. **Decide, then draft the changes with the LLM.** For the points you accept, have the AI help implement them — you specify exactly what and why. Your spec is the prompt; precise in, correct out.
4. **Verify — non-negotiable.** Re-run your own checks (`scripts/recalc.py`, the parity tie-out, sensitivity continuity, no error cells) and confirm the numbers before you commit. An AI will hand you a confident wrong edit; verification is what makes the result *yours*.
5. **Close the loop on the PR.** Reply in the thread with what you changed, what you pushed back on and why, then commit and push. Writing down the reasoning is exactly how this works on a real team.

*This is the same human-in-the-loop discipline the whole project is built on: the LLM drafts, you edit and verify, and you own the result.*
