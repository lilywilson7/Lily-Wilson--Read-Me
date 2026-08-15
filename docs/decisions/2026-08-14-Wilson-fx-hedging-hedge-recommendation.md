# FX Hedge Recommendation

**To:** Chief Financial Officer  
**From:** Lily Wilson  
**Date:** August 14, 2026  
**Subject:** EUR Receivable FX Hedging Recommendation

## A. Exposure Summary

The company has a €20 million foreign-currency receivable that will be converted into U.S. dollars at settlement. The primary financial risk is that EUR/USD movements could materially change the dollar value of the receivable before settlement.

Using the August 14, 2026 market-data inputs, the spot rate is 1.1567 USD/EUR. The relevant USD interest rate is 3.80% and the EUR interest rate is 2.943%, producing a CIP-implied one-year forward rate of approximately 1.16646 USD/EUR.

The objective is therefore to reduce uncertainty in the dollar value of the receivable while considering the trade-off between certainty, upside participation, liquidity, financing requirements, and option premium cost.

## B. Hedge Outcomes

### Unhedged

The unhedged position provides full participation in EUR/USD movements but exposes the company to the greatest cash-flow uncertainty.

At the 95% settlement-spot scenario, the receivable produces approximately $21.98 million.

At the 100% scenario, it produces $23.134 million.

At the 105% scenario, it produces $24.291 million.

This range illustrates both the opportunity and the risk of remaining unhedged. A stronger euro increases dollar proceeds, but a weaker euro materially reduces them.

### Forward Hedge

The forward hedge locks in approximately:

**$23,329,187.81**

The forward therefore eliminates settlement-date FX uncertainty. At the current spot baseline, this is approximately $195,188 more than the unhedged spot-equivalent value.

The major advantage is certainty without an option premium. The primary trade-off is that the company gives up additional upside if the euro appreciates beyond the forward rate.

### Money-Market Hedge

The money-market hedge produces the same locked result as the forward:

**$23,329,187.81**

The three steps are:

1. Borrow EUR today.
2. Convert the borrowed EUR to USD at spot.
3. Invest the USD through settlement.

The result reconciles to the forward because the inputs satisfy covered interest-rate parity.

Although financially equivalent, the money-market hedge requires financing and balance-sheet capacity. The forward is therefore operationally simpler if the company has adequate bank forward capacity.

### Put Hedge

The put provides downside protection while allowing the company to participate in EUR appreciation above the strike.

Using the workbook's direct scenario premium of $0.019/EUR, the premium cost is:

**$380,000**

At or below the strike, the gross protected proceeds are $23.134 million before the premium, producing net proceeds of:

**$22.754 million**

Above the strike, the company participates in the higher EUR/USD rate, less the premium.

The put therefore offers flexibility that the forward does not, but that flexibility comes at a significant premium cost.

### Call Participation

The call analysis requires additional care because the Stage 2 specification presents the call as a future-payable variant rather than the primary hedge for the current EUR receivable.

Accordingly, the call should not be treated as directly interchangeable with the forward and put strategies for this receivable without an explicit specification of the intended receivable-side structure.

## C. Sensitivity Interpretation

The sensitivity analysis demonstrates the fundamental trade-off between certainty and flexibility.

When EUR/USD depreciates, the unhedged receivable loses dollar value. The forward and money-market strategies remain fixed at approximately $23.329 million, protecting the company from this downside.

The put also establishes a floor, but the premium reduces the protected proceeds. Its advantage is that it preserves participation when EUR/USD appreciates.

When EUR/USD appreciates substantially, the unhedged position produces the greatest proceeds because it retains unlimited upside. The put participates in that appreciation after the strike, while the forward remains locked.

The decision therefore depends on the firm's risk posture. A company prioritizing budget certainty will favor the forward or money-market hedge. A company willing to pay for upside participation may prefer the put.

## D. Recommendation

### Recommended strategy: Forward hedge

I recommend the **forward hedge** for the €20 million receivable.

The primary reason is that the forward provides the desired cash-flow certainty at approximately **$23.329 million** without requiring the company to pay the approximately **$380,000** option premium associated with the put.

The forward also eliminates the financing and balance-sheet considerations associated with constructing the money-market hedge while producing the same locked proceeds under covered interest-rate parity.

The put is defensible if management places a high value on retaining upside participation in a stronger euro. However, the available project information does not establish that upside participation is more important than cost and certainty. Given the objective of reducing FX-related cash-flow uncertainty, the forward is the stronger base-case recommendation.

## E. Executive Justification

The recommendation prioritizes five considerations.

**Cash-flow stability.** The forward converts a variable EUR receivable into a known dollar amount, making operating and budgeting decisions more predictable.

**Budget certainty.** The $23.329 million locked proceeds provide a clear planning figure regardless of the settlement spot rate.

**Liquidity.** Unlike the put, the forward does not require an option premium of approximately $380,000 under the workbook's scenario assumptions.

**Optionality.** The principal disadvantage of the forward is the loss of upside participation if the euro appreciates. The put preserves that optionality but requires the company to purchase it through the premium.

**Financing requirements.** The money-market hedge is economically equivalent to the forward under covered interest-rate parity but requires borrowing EUR and investing USD. The forward is operationally simpler when suitable bank credit capacity is available.

Overall, the forward offers the best balance of certainty, cost, and operational simplicity for the stated risk objective. The put would become more attractive if management explicitly prioritized EUR appreciation upside and accepted the associated premium cost.

## Conclusion

For the current €20 million receivable, the forward hedge should be the base-case strategy.

It locks approximately **$23.329 million**, matches the money-market hedge under covered interest-rate parity, avoids the put premium, and materially reduces the company's exposure to adverse EUR/USD movements.

The recommendation should be revisited only if management's risk preference changes toward preserving substantial EUR appreciation upside or if forward credit capacity is constrained.