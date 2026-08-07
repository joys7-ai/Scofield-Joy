# Stage 3 review — aerospace build & audit · Treasury sign-off

Joy — this is the best audit note in the cohort, and I want to be precise about why. Most build audits re-read the workbook and confirm it looks fine. Yours went hunting, found a bug that was actively lying to you, and then did the harder thing: it correctly refused to "fix" something that wasn't broken. That is the difference between checking and auditing.

| Criterion | Score |
|---|---|
| Contract compliance | 49.6 / 50 |
| Structure & presentation | 25 / 25 |
| Audit note | 25 / 25 |
| **Total** | **100 / 100** |

**What you did well — and why it matters**

- **You caught a silent error, which is the hardest class to catch.** `USD_FLOOR_PUT` pointed at `Options!D17`, a blank cell one row above the real `=MIN(D6:D16)`. Your note makes the key observation explicitly: a blank cell reads as **0 in a reference, not as `#REF!`** — so the summary block showed `$0` with no error flag anywhere. Errors that announce themselves get fixed by anyone. Errors that quietly return a plausible number are what actually reach a CFO, and you found one by tying the summary back to its source instead of trusting it.
- **You diagnosed the parity gap instead of forcing it closed.** `F_implied` at 1.1684 against a course-assigned `F0_in` of 1.0935 is a ~$1.5M spread between Forward and Money Market. The tempting move is to nudge an input until the check goes green. You recomputed `F_implied` by hand outside the workbook, got the same number, and concluded the formula was right and the *inputs* were not mutually consistent. Reproducing a suspect number independently is exactly how you establish that a variance is real.
- **You protected the reader from your own output.** Flagging that Money Market dominates the entire sensitivity grid *because of* the parity gap — and that this must not be read as evidence Money Market genuinely beats Forward — is judgment, not modeling. A number that is correct but easy to misread is still a hazard, and you labeled it.
- **The kink verification shows the payoff is right, with arithmetic.** Confirming put proceeds at `S_T = K_PUT` equal `K_PUT × FC_AMT + FV_PREM_PUT`, flat below the strike and rising linearly above, is the check that proves an option payoff is actually piecewise — not just plotted to look that way.

**To push it further (real-desk nuance)**

- **Your $15k-scale intuition was right; Stage 4 proved it.** You predicted the gap would close once the four inputs came from one consistent dataset. It did — to about $231 on a $23.4M notional. Worth internalising: that is what a *real* parity residual looks like. Anything materially wider is a data problem or an arbitrage, not rounding.
- **The step-index column is typed, not driven.** `Options!A6:A16` and `Sensitivity!A13:A23` hold literal −5…+5. It's defensible as an axis, but a hand-typed grid is the thing that silently goes out of sync when someone widens the range later. Drive it from a single step-count and step-size input so the grid can't disagree with itself.
- **Fix the filing.** The note sits at `analysis/analysis/2026-08-05-Scofield-build-audit.md` — a doubled directory. It reads as a path pasted into a filename. Trivial to fix, but on a real team the person looking for your audit trail has to find it.
- **Name what you would have missed.** Your note tells me what you checked. The senior version also says what you *didn't* check and why — e.g. you never independently validated the option premiums, because they're course-assigned. Stating the boundary of an audit is what stops a reader over-trusting it.

**Next — Stage 4**

Already in and reviewed separately — and the parity prediction from this note is exactly what it confirmed. Carry the same instinct into Stage 5: when the LLM hands you a recommendation, the question is not "is this plausible" but "which number here would I have to recompute by hand before I'd sign it."

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
