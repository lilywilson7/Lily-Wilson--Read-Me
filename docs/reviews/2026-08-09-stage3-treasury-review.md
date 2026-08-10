# Stage 3 review — aerospace build & audit · Treasury sign-off

Lily — Findings 3, 4, and 5 are the real work here, and they share a theme most students never notice. Your money-market steps referenced intermediate cell addresses; your sensitivity Forward column pointed at `Forward!$B$3`; your Money Market column pointed at `Money_Market!$B$5`. All three *computed correctly*. Nothing was broken. You replaced them with named-range formulas anyway.

That is the right call and it is a hard one to make, because the payoff is invisible today. A cross-sheet cell reference works perfectly until someone inserts a row on the Forward tab, at which point `$B$3` silently points at something else and your sensitivity table starts producing confident, wrong numbers with no error cell to warn anyone. Hunting down working-but-fragile references is maintenance discipline, and it is why the named-range contract exists in the first place.

| Criterion | Score |
|---|---|
| Contract compliance | 50 / 50 |
| Structure & presentation | 25 / 25 |
| Audit note | 25 / 25 |
| **Total** | **100 / 100** |

**What you did well — and why it matters**

- **Five findings in a checked / found / fixed structure**, four of them real defects with a stated remedy. That is well above the three-substantive-findings bar, and the structure makes each one independently verifiable.
- **You filled corrected formulas through the whole table.** Both sensitivity fixes note that the corrected formula was "filled through the sensitivity table" — not patched in one row. Partial fixes that leave three of eleven rows on the old formula are a classic way to make a table worse than before it was touched.
- **Your final validation is a checklist against the build contract.** Ten named ranges, three money-market steps, parity, premiums included, 95–105% in 1% increments, formula-driven, chart present, no error cells. Closing the audit by walking the contract line by line is exactly right.
- **Finding 2 confirms attachment, not just existence.** "Present and attached to the intended input cells" — a named range that exists but points at the wrong cell is worse than one that is missing, because it fails silently.

**The one finding that needs a second look — Finding 1**

Your parity check failed: implied forward 1.114955872 against `F0_in` of 1.0935. Your fix was to change `R_FC` from **3.3% to 5.3%**, "so the placeholder rate assumptions are internally consistent with the placeholder forward." The parity difference went to 0 and the check now reads PASS.

The arithmetic works. The economics do not. A 5.3% one-year euro interest rate is not a plausible number — euro-area one-year yields have been in the 2–3% range, and 5.3% would be roughly double the observable market. What you have done is make a validation pass by adopting an input that could not occur.

The deeper issue is the direction of the reasoning. You treated the scenario's `F0_in` as fixed and solved for a rate that justified it. But a forward *below* spot implies euro rates *above* dollar rates, and that is simply not the current environment — one-year USD yields are near 4% and euro yields near 2.5%, so the euro should trade at a forward premium. The assigned 1.0935 is stale relative to today's spot near 1.15. It is the forward that is wrong, not your rates.

Compare with how a classmate on the same problem handled it: same failing check, same diagnosis that "the placeholder assumptions rather than the hedge formulas" were at fault — but she adjusted toward *plausible* rates and documented the residual, rather than forcing the difference to exactly zero.

**Why this matters at Stage 4:** the option you used here disappears. Every input comes from a market and none of them is yours to tune. When you populate live data, your spot will be near 1.15 and your euro rate near 2.5%, and the CIP forward will land near 1.17 — nowhere near 1.0935. The correct response is to use the CIP-implied forward, document the gap against the scenario value as a finding, and explain that the scenario's forward was set in a different rate environment. Do not adjust live market rates to make it reconcile.

None of this costs you points — you found a real inconsistency and dealt with it transparently, which is more than most. But "make the check pass" and "make the model right" are different objectives, and Finding 1 optimized for the first.

**Next — Stage 4**

Not yet in. Pull live spot, a one-year Treasury CMT (FRED `DGS1`) for `R_USD`, and the ECB euro-area one-year yield-curve point or 12-month Euribor for `R_FC` — tenor-matched on both legs. Then let the parity gap against 1.0935 be your headline finding rather than something to engineer away.

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
