# Stage 2 review — aerospace · Treasury sign-off

Joy — I read this the way Treasury actually reads a spec: as the contract the build must honor. Hand this to a colleague or an AI and they could rebuild the workbook without asking you a single question. That is the bar, and you cleared it. After earlier stages, this is real growth — and it earned the mark on merit, not generosity.

| Criterion | Score |
|---|---|
| Named-range contract & tab architecture | 30 / 30 |
| Calculation flow | 30 / 30 |
| Validation & sensitivity plan | 20 / 20 |
| Reproducibility & prompt log | 20 / 20 |
| **Total** | **100 / 100** |

**What you did well — and why it matters**

- **You got the exposure direction right, and the whole model follows from it.** A EUR 20M receivable settling in 365 days is hurt when EUR *depreciates* against the dollar — fewer USD per euro at settlement. Your hedges answer exactly that risk: sell EUR forward (`USD_FWD = FC_AMT × F0_in`) and buy a EUR put floor (`MAX(S_T, K_PUT) × FC_AMT + FV_PREM_PUT`). When the direction is wrong, every downstream number is wrong; yours is coherent top to bottom.
- **You carried the put premium forward at `R_USD` to `FV_PREM_PUT`.** That is the discipline most students miss — you can't net an upfront cost against settlement-date proceeds without putting them on the same time footing. Getting the carry right is what makes the strategy comparison honest.
- **You pre-declared the parity gap instead of hiding it.** Flagging that `USD_MM ≈ USD_FWD` *won't* hold at Stage 2 because `F0_in` is course-assigned — and will only close once live data makes the four inputs mutually consistent — is exactly how a real analyst protects a check figure from being read as a bug. That is treasury judgment, not just modeling.

**To push it further (real-desk nuance)**

- **A live forward has basis, not just parity.** When you load Stage 4 quotes, the market forward will differ from your covered-parity number by the cross-currency basis and the dealer's spread. Note the residual — don't force it to zero. And if the gap is *large*, that's not noise to hide: it means the forward and money-market hedges lock materially different USD, so flag the advantaged leg (or a genuine arbitrage) and choose it rather than assuming the two must tie.
- **Interrogate your ATM strike.** `K_PUT` at spot buys full protection at the highest premium. Price an OTM put too (a lower strike, a cheaper floor further from spot): that shows the CFO the cost-of-protection curve, not one point on it.
- **Don't let the ex-post winner drive the recommendation.** Whichever strategy "wins" on your grid is hindsight. Frame Stage 5 on risk tolerance — the forward's certainty versus the put's asymmetric upside — not on which line happened to land highest.

**Next — Stage 3**

Build straight from §4, then audit against your own §6.3 checklist: every calculated cell a named-range formula (no bare `$F$n` references), all three families live, the sensitivity table and kinked-payoff chart in, and a build note with at least three findings. Your §6.2 known-risks register already tells you where the build will strain — hold yourself to it.

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
