# Stage 2 review — aerospace · Treasury sign-off

Lily — you named the model, not just the deliverable. "This document is the build specification for Phase 3" tells a reader what the spec is *for* — it is a prompt and a contract, not a description written after the fact. And your closing objective sentence is the right discipline: "The final hedge recommendation will be made only after the model is populated with live market data and independently validated in later project stages." You refused to pre-decide the answer.

| Criterion | Score |
|---|---|
| Named-range contract & tab architecture | 30 / 30 |
| Calculation flow | 30 / 30 |
| Validation & sensitivity plan | 20 / 20 |
| Reproducibility & prompt log | 20 / 20 |
| **Total** | **100 / 100** |

**What you did well — and why it matters**

- **You translated the exposure into a dollar figure immediately.** "At the assigned indicative one-year forward rate of 1.0935 USD/EUR, the receivable has an indicative locked value of $21.87 million." A CFO now knows the size of the decision from the first paragraph.
- **You listed what the trade-offs actually are.** "Certainty, downside protection, upside participation, financing requirements, and option premium cost." Most specs name two or three; including *financing requirements* is the one people forget, and it is precisely why the money-market hedge is rarely chosen in practice even when it prices identically.
- **You named the specific model you used.** "GPT-5.6 Luna — drafting assistance; human review and correction by Lily Wilson." Naming the model and the division of labour is what makes AI-assisted work auditable. "Used AI" tells a reader nothing.
- **You versioned at 1.0 with created and updated fields.** Keep incrementing as the spec changes at Stages 3 and 4 — your Stage 3 audit made real changes to the assumption set, which is a version bump.
- **You wrote the indicative-labelling rule as a requirement on the build**, not just a note to yourself: placeholders "must be labeled 'indicative — replaced with live market data at Stage 4.'"

**Two things to tighten**

**1. `S0_in` has no value.** Your placeholder reads `[Stage-2 market-close spot]` — a bracket, not a number. Every other core input carries one. That single gap has consequences: without a spot you cannot compute a parity-implied forward, so you cannot check whether your rate placeholders reconcile with the assigned forward of 1.0935 — and they do not, which is what your Stage 3 audit discovered the hard way (see that review). Ten minutes of arithmetic in §2 would have surfaced it before you built anything.

**2. Pre-commit the parity tolerance as a number.** How close must the money-market result sit to the forward before you call it a pass — 0.05% of notional, $5,000, a basis point on the rate? A tolerance decided after you see the answer is not a control. This matters especially for you, because at Stage 3 you resolved a parity break by adjusting an input, and a pre-committed tolerance is what tells you whether a gap is noise to absorb or a finding to report.

**A heads-up for Stage 4**

Your scenario's forward is 1.0935, and live EUR/USD is currently trading near 1.15. A forward *below* spot implies EUR rates exceed USD rates — which is not the current environment; one-year USD yields are around 4% and euro-area one-year yields around 2.5%, so the euro should trade at a forward *premium*, above spot.

This means the assigned 1.0935 will not reconcile with live market data at Stage 4, and that is not your error to fix — it is a stale scenario input. The right move is to compute the CIP-implied forward from your live spot and live rates, use that as `F0_in`, and *document the gap* against the scenario value as a finding. Do not adjust live market rates to make the scenario's forward work.

**Next — Stage 3**

Already in and reviewed separately. Your build audit found four real defects, which is genuinely strong — I take up the one that needs a second look in that review.

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
