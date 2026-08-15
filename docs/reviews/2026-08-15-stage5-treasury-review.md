# Stage 5 review — aerospace LLM analysis & validation · Treasury sign-off

Lily — you wrote the best prompt in this cohort, and it is worth saying why before anything else:

> *"Do not attempt to match an expected answer. Do not infer or use results from an unseen workbook. If the documents leave something ambiguous, explicitly identify the ambiguity and state the assumption you make."*

Most people asked a model to compute. You designed an **experiment**. Those three sentences remove the two failure modes that quietly ruin an independence test — a model reverse-engineering the answer it thinks you want, and a model papering over a gap in your spec instead of reporting it. And it worked exactly as designed: the run surfaced a genuine spec ambiguity on the call, because you had explicitly licensed it to say "the documents do not define this" rather than invent something. That is experimental design, not prompting.

| Criterion | Score |
|---|---|
| LLM execution & comparison | 25 / 25 |
| Hand verification | 25 / 25 |
| Recommendation & executive voice | 25 / 25 |
| Spec retrospective | 17 / 17 |
| Repo polish | 6.4 / 8 |
| **Total** | **99 / 100** |

**What you did well — and why it matters**

- **Every number on both sides of the disagreement is correct.** I recomputed all of it: `20,000,000 / 1.02983868 = €19,420,516.1` ✓ → `× 1.1567 = $22,463,711.0` ✓ → `× 1.03852778 = $23,329,187.8` ✓, and the CIP forward `1.1567 × 1.03852778 / 1.02983868 = 1.1664594` ✓ ties to ten decimals. Claude's put figure of $22,739,359.44 and your workbook's $22,754,000 are *both* arithmetically right — they differ only in convention, which is precisely what makes the next section worth reading.
- **You committed the raw output and the exact prompt.** `analysis/2026-08-14-Wilson-llm-output.md`, plus the verbatim prompt in the validation doc, plus an explicit statement of what was withheld: *"No workbook, prior calculations, screenshots, or previous ChatGPT conversation was provided."* A reader can reconstruct your experiment. That is the standard, and roughly half the cohort did not meet it.
- **You let the model's refusal stand as a finding.** *"Rather than inventing a receivable call structure, Claude explicitly stated its assumption."* Correct read — a model saying "underspecified" is a well-behaved model, and treating that as evidence about your spec rather than as a failure to answer is the right instinct.
- **Your money-market section explains convergence rather than asserting it.** *"The money-market hedge therefore reconciles to the forward hedge"* — with the three steps shown and the parity reason given, not just the matching totals.

**The one substantive correction — Claude was right and you marked it wrong**

This is the most consequential call in your document, and it is inverted:

> *"Claude instead future-valued the premium using the USD accumulation factor and calculated a settlement cost of $394,640.56… This is diagnosed as an **LLM calculation/interpretation error** rather than a workbook error."*

It is not an LLM error. The future-valued treatment is the correct one, and the workbook is the side that is wrong.

The reasoning is the same one you applied correctly forty lines earlier in the money-market hedge. The premium is paid **today**. The receivable settles in **365 days**. Netting an undiscounted $380,000 against settlement-date proceeds subtracts a today-dollar from a future-dollar — the exact mismatch your Step 1/Step 3 chain exists to avoid, since it discounts the euro leg at `R_FC` and compounds the dollar leg at `R_USD` for precisely this reason. If timing matters enough to build a three-step money-market hedge around it, it matters for the premium too.

Two things make this more than a judgment call:

1. **The formula Claude used is your own spec's formula.** You quote it directly: `FV_PREM_PUT = -PREM_PUT × FC_AMT × DF_USD`. Claude did not invent a convention — it read the one your Stage 2 specification defines and applied it. Your **workbook** is the artifact that departed from the spec. So the correct diagnosis is not "LLM error" but *"workbook does not implement the spec's stated premium formula"* — a spec-to-build inconsistency, and the most valuable class of finding this exercise can produce.
2. **The check confirms it.** `380,000 × 1.03852778 = $394,640.56` ✓ — exactly Claude's number, exactly your $14,640.56 difference.

Corrected, your put floor is **$22,739,359.44** rather than $22,754,000, and the breakeven against the forward moves from `(23,329,187.81 + 380,000)/20,000,000 = 1.18546` to `(23,329,187.81 + 394,640.56)/20,000,000 = **1.18619**` — from 2.49% to 2.55% above spot. Small in dollars. Large in what it says about your model.

Your retrospective is already halfway there — *"The specification should explicitly state whether option premiums are reported as a payment today or as a settlement-date-equivalent cost"* — but it frames this as a gap the spec left open. Your spec did not leave it open; it gave the formula, and the workbook diverged. The v2 fix is therefore not "state the convention" but **"add a validation rule that ties each workbook output back to the spec formula it implements."** That is what would have caught this before an outside model did.

I want to be clear this is not a deduction — you found the discrepancy, quantified it to the cent, and wrote it up. The finding is yours. Only the direction of the arrow is wrong, and getting that arrow right is the difference between "the model I hired made a mistake" and "my build drifted from my own specification."

**One smaller item — the call row mixes payoffs with proceeds**

In your comparison tables the call column shows workbook values of **−$480,000**, **−$480,000**, and **$676,700**, sitting alongside four strategies quoted as total USD proceeds in the $22–24 million range. Those look like option *payoffs* net of premium, not proceeds. A reader scanning the table sees a strategy that appears to return negative twenty-three million dollars less than every alternative.

Given you and Claude both concluded the call is not a receivable hedge, the cleanest fix is to drop it from the proceeds comparison entirely and discuss it in prose — or, if it stays, label the row *"(payoff, not proceeds)"* so the units are unambiguous.

**One framing note**

§B: *"At the current spot baseline, this is approximately $195,188 more than the unhedged spot-equivalent value."* Arithmetically right (23,329,187.81 − 23,134,000 ✓), but it compares settlement-date dollars against today's dollars, and the gap is the USD-EUR interest differential rather than value the forward creates — your own money-market replication reaches the identical figure with no forward involved at all. You pivot immediately to the correct reason (*"certainty without an option premium"*), so nothing downstream breaks. Just label it as carry.

While you are in §D: the memo never states the crossover. `S_T* = 1.18619`, **2.55% above spot** — the euro has to rally 2.5% before the put is worth what it costs. That one number does more work for a CFO than the whole sensitivity table.

**Repo polish — 1.6 points, one item**

`LICENSE` is the only open box; description, per-directory READMEs, public status and commit history are all in place. Add an MIT license at the repo root for a 100.

Separately: your Stage 4 market-data work landed on 2026-08-14 and has now been graded at **100**. It came in after the previous grading pass, which is why you had not heard anything on it.

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
