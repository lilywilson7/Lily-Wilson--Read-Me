# U.S. Aerospace Manufacturer — FX Transaction Hedge Model · Technical Specification

> Technical specification for the FX transaction hedge model — the named-range contract, calculation flow, sensitivity design, outputs, and validation checks. This document is the build specification for Phase 3.

| Field | Value |
|------|------|
| **Created by** | Lily Wilson |
| **Updated by** | Lily Wilson |
| **Date Created** | 2026-08-08 |
| **Date Updated** | 2026-08-08 |
| **Version** | 1.0 |
| **LLM Used** | GPT-5.6 Luna — drafting assistance; human review and correction by Lily Wilson |
| **Role** | Treasury Analyst / FP&A Analyst |
| **Audience** | CFO / Director of Treasury |
| **Scenario** | U.S. Aerospace Manufacturer — Scenario 4 |
| **Scenario slug** | aerospace |

---

## 1. Problem Statement

The U.S. Aerospace Manufacturer expects a EUR-denominated receivable of €20,000,000 settling one year from the Stage 2 model inception date. The firm's functional currency is USD, so the USD value of the receivable depends on the EUR/USD exchange rate at settlement. A decline in EUR/USD reduces the USD proceeds received and creates uncertainty in cash-flow planning and budgeted revenue. At the assigned indicative one-year forward rate of 1.0935 USD/EUR, the receivable has an indicative locked value of $21.87 million.

The model will compare four outcomes: no hedge, forward hedge, money-market hedge, and EUR put-option hedge. The objective is to quantify the trade-offs between certainty, downside protection, upside participation, financing requirements, and option premium cost. The final hedge recommendation will be made only after the model is populated with live market data and independently validated in later project stages.

---

## 2. Inputs — Named-Range Contract

Every input must be exposed as a named range. The standardized names below must be used exactly. Scenario and market values are placeholders for Phase 2 and must be labeled "indicative — replaced with live market data at Stage 4."

| Named Range | Description | Placeholder Value | Unit | Stage-4 Data Source |
|---|---|---:|---|---|
| `FC_AMT` | Foreign-currency receivable | 20,000,000 | EUR | Scenario assignment; transaction amount is fixed |
| `S0_in` | Spot EUR/USD at model inception | [Stage-2 market-close spot] | USD/EUR | Live EUR/USD spot from a market-data source such as Bloomberg, Yahoo Finance, or equivalent; record timestamp |
| `F0_in` | One-year forward EUR/USD | 1.0935 | USD/EUR | Scenario placeholder replaced with live one-year EUR/USD forward quote from market data/provider |
| `R_USD` | USD annual interest rate | [Stage-2 market rate] | Annual % |
| `R_FC` | EUR annual interest rate | [Stage-2 market rate] | Annual % |
| `K_PUT` | EUR put strike | [set at/near S0_in] | USD/EUR |
| `K_CALL` | EUR call strike | [set at/near S0_in] | USD/EUR |
| `PREM_PUT` | EUR put premium per EUR | 0.019 | USD/EUR | Scenario placeholder; replace with live option premium/quote at Stage 4 |
| `PREM_CALL` | EUR call premium per EUR | 0.024 | USD/EUR | Scenario placeholder; replace with live option premium/quote at Stage 4 |
| `T_DAYS` | Days to settlement | 365 | Days | Derived from transaction and settlement dates; confirm exact business-day maturity at Stage 4 |

All market-related placeholder values above are **indicative — replaced with live market data at Stage 4**. The scenario amount and one-year maturity are transaction assumptions rather than market prices.

### Derived / Intermediate Values

The workbook should also calculate:

- `DF_USD` = `1 + R_USD × T_DAYS / BASIS`
- `DF_FC` = `1 + R_FC × T_DAYS / BASIS`
- `FV_PREM_PUT` = `−PREM_PUT × FC_AMT × DF_USD`
- `FV_PREM_CALL` = `−PREM_CALL × FC_AMT × DF_USD`
- `S_T_grid` = settlement spot sensitivity grid from 0.95 × `S0_in` through 1.05 × `S0_in`
- `USD_NO_HEDGE` = `S_T × FC_AMT`

For the base specification, `BASIS = 360` is used consistently to match the assignment's ACT/360 calculation convention. The Phase 3 build must expose the basis assumption visibly in the Notes & Assumptions tab.

---

## 3. Tab Architecture

The workbook must contain the following tabs in this order:

### Cover
Identifies the company, Scenario 4, EUR receivable, model purpose, version, author, and model status. The Cover tab must clearly state that Phase 2 values are indicative placeholders and will be replaced by live market data at Stage 4.

### Legend_Key
Explains input-cell conventions, output-cell conventions, named ranges, quote direction, hedge abbreviations, and signs. It should state that EUR/USD is quoted as USD per EUR and that a higher EUR/USD rate represents EUR appreciation.

### Inputs
Contains the complete named-range input table. Every adjustable market input must have its name, value, unit, source, date, and placeholder/live status visible.

### No_Hedge
Calculates the unhedged EUR receivable proceeds across the settlement-spot sensitivity grid. This is the benchmark against which hedge value is measured.

### Forward
Calculates the locked USD proceeds using `FC_AMT × F0_in`. The result is constant across the settlement-spot sensitivity grid.

### Money_Market
Shows the three required steps separately: EUR borrowing, spot conversion, and USD investment. It also contains the covered-interest-parity check against the forward hedge.

### Options
Calculates the EUR put hedge across the settlement-spot grid, including the put payoff and future value of the premium. The call inputs remain available for the standardized contract and future payable variant.

### Sensitivity
Combines no hedge, forward, money market, and put-option proceeds into one comparison table and one line chart.

### Notes_Assumptions
Records data sources, timestamps, day-count basis, transaction-cost treatment, parity assumption, premium treatment, excluded risks, and model limitations.

---

## 4. Assumptions & Constraints

### Quote convention
All FX rates are expressed as USD per EUR. A higher EUR/USD rate means the euro appreciates relative to the dollar. Because the firm has a EUR receivable, EUR depreciation reduces USD proceeds.

### Timing
The model assumes a one-year settlement horizon represented by `T_DAYS = 365`. The exact settlement date will be confirmed when live data are populated in Stage 4.

### Day-count convention
The base model uses ACT/360. Interest accumulation is represented as `1 + rate × T_DAYS / 360`. This convention must be applied consistently to both money-market legs and to the future value of option premiums.

### Covered interest-rate parity
The money-market hedge is expected to approximate the forward hedge under covered interest-rate parity. Any persistent difference beyond normal rounding or market-basis differences must be investigated.

### Transaction costs
Broker fees, bid-ask spreads, credit charges, and other transaction costs are excluded from the base case. Stage 4 should document any available transaction-cost information and incorporate it if material.

### Option premium
The put premium is paid upfront in USD per EUR of notional. The premium is treated as a negative cash flow and carried forward to settlement using `R_USD` so that it is comparable with settlement-date USD proceeds.

### Counterparty and credit risk
Counterparty default risk, collateral requirements, and credit valuation adjustments are excluded from the base model.

### Taxes and accounting
Tax effects, hedge-accounting treatment, and financial-statement presentation are excluded. The workbook reports pre-tax economic cash outcomes.

### Sensitivity methodology
Future spot is varied deterministically rather than probabilistically. No probability weights, Monte Carlo simulation, or volatility forecast is required in this stage.

---

## 5. Calculation Flow

All calculation instructions must use named ranges. Cell addresses must not be used in formulas or build instructions.

### 5.1 No-Hedge Benchmark

For each settlement spot `S_T`:

`USD_NO_HEDGE(S_T) = S_T × FC_AMT`

This represents the USD proceeds if the firm leaves the EUR receivable completely unhedged.

### 5.2 Forward Hedge

The forward hedge locks the EUR sale rate today.

`USD_FWD = FC_AMT × F0_in`

`USD_FWD` must remain constant regardless of `S_T`.

### 5.3 Money-Market Hedge

The money-market hedge must be displayed as three separate calculation steps.

**Step 1 — Borrow EUR today**

`Borrow_FC = FC_AMT / (1 + R_FC × T_DAYS / 360)`

The borrowing amount is sized so that principal plus EUR interest equals the €20,000,000 receivable at settlement.

**Step 2 — Convert EUR to USD at spot**

`USD_NOW = Borrow_FC × S0_in`

**Step 3 — Invest USD until settlement**

`USD_MM = USD_NOW × (1 + R_USD × T_DAYS / 360)`

At settlement, the EUR receivable repays the EUR borrowing. The USD investment is therefore the locked USD proceeds.

### 5.4 Covered Interest-Rate Parity Check

Calculate the forward rate implied by the spot and interest rates:

`F_IMPLIED = S0_in × (1 + R_USD × T_DAYS / 360) / (1 + R_FC × T_DAYS / 360)`

The model must compare `F_IMPLIED` with `F0_in`.

Expected result:

`F_IMPLIED ≈ F0_in`

The corresponding proceeds should also satisfy:

`USD_MM ≈ USD_FWD`

Any material unexplained difference must be flagged for audit.

### 5.5 EUR Put-Option Hedge

The firm buys a EUR put to protect the USD value of the receivable.

The put payoff at settlement is:

`PUT_PAYOFF(S_T) = MAX(0, K_PUT − S_T) × FC_AMT`

Total gross USD proceeds are:

`GROSS_PUT_PROCEEDS(S_T) = S_T × FC_AMT + PUT_PAYOFF(S_T)`

The net option proceeds, including the future value of the premium, are:

`USD_PUT(S_T) = MAX(S_T, K_PUT) × FC_AMT + FV_PREM_PUT`

where:

`FV_PREM_PUT = −PREM_PUT × FC_AMT × (1 + R_USD × T_DAYS / 360)`

The put therefore establishes a floor while allowing the company to participate in favorable EUR appreciation above the strike, subject to the premium cost.

### 5.6 Call Option Variant

`K_CALL` and `PREM_CALL` must remain in the named-range contract because they are part of the standardized model and will support the payable variant later in the project.

For a EUR payable, the call would protect against EUR appreciation. It is not the primary hedge for the current EUR receivable.

---

## 6. Sensitivity Plan

The sensitivity analysis must vary the settlement EUR/USD spot rate `S_T` from:

`0.95 × S0_in` through `1.05 × S0_in`

using 1% increments.

The grid therefore contains the following relative levels:

- 95%
- 96%
- 97%
- 98%
- 99%
- 100%
- 101%
- 102%
- 103%
- 104%
- 105%

For each `S_T`, calculate:

1. `USD_NO_HEDGE`
2. `USD_FWD`
3. `USD_MM`
4. `USD_PUT`
5. Hedge profit for each active hedge versus no hedge

The forward and money-market proceeds should remain constant across the grid. The no-hedge proceeds should move directly with `S_T`. The put strategy should show a floor below `K_PUT` and upside participation above `K_PUT`, reduced by the premium.

The Sensitivity tab must contain one line chart showing USD proceeds on the vertical axis and `S_T` on the horizontal axis for all four strategies.

The chart's purpose is to let the CFO immediately see the trade-off between certainty and upside: no hedge has the greatest FX exposure, forward and money market lock the proceeds, and the put provides downside protection while retaining favorable upside.

---

## 7. Validation Rules

The completed Phase 3 workbook must pass all of the following checks.

### Check 1 — Forward calculation
`USD_FWD` must equal:

`FC_AMT × F0_in`

### Check 2 — Money-market pipeline
The three money-market steps must calculate without circular references or hard-coded results.

### Check 3 — Parity
`F_IMPLIED` must approximately equal `F0_in` when the interest-rate inputs and forward quote are internally consistent.

### Check 4 — Hedge proceeds
`USD_MM` must approximately equal `USD_FWD`, subject to rounding and documented market-input differences.

### Check 5 — Put continuity
The put proceeds must be continuous across the sensitivity grid. At `S_T = K_PUT`, the put payoff should be zero before premium treatment.

### Check 6 — Put floor
For `S_T < K_PUT`, the gross put-protected proceeds must equal `K_PUT × FC_AMT` before the premium cost.

### Check 7 — Upside participation
For `S_T > K_PUT`, the put payoff must be zero and the strategy must participate in the higher market exchange rate, less the premium.

### Check 8 — No errors
The workbook must contain no `#REF!`, `#DIV/0!`, `#VALUE!`, `#NAME?`, or similar spreadsheet errors.

### Check 9 — Formula integrity
Every calculated output must be formula-driven. No summary result may be manually typed into an output cell.

### Check 10 — Sensitivity completeness
The settlement-spot grid must contain exactly the specified 95% through 105% range in 1% increments.

### Check 11 — Named-range integrity
All ten required standardized names must exist and must be used consistently in the calculation logic.

These checks become the Phase 3 AI-build audit checklist.

---

## 8. Outputs

The workbook must produce the following named outputs:

| Output | Definition | Purpose |
|---|---|---|
| `USD_NO_HEDGE` | `S_T × FC_AMT` | Unhedged USD proceeds |
| `USD_FWD` | `FC_AMT × F0_in` | Locked forward proceeds |
| `USD_MM` | Three-step money-market proceeds | Locked money-market proceeds |
| `USD_PUT(S_T)` | Put-protected proceeds net of premium | Option proceeds by spot scenario |
| `F_IMPLIED` | CIP-implied forward rate | Parity validation |
| `USD_FLOOR_PUT` | Minimum `USD_PUT` across sensitivity grid | Downside protection measure |
| `USD_BASE_NO_HEDGE` | No-hedge proceeds at `S_T = S0_in` | Baseline comparison |
| `USD_BASE_FWD` | Forward proceeds at baseline | Baseline comparison |
| `USD_BASE_MM` | Money-market proceeds at baseline | Baseline comparison |
| `USD_BASE_PUT` | Put proceeds at baseline | Baseline comparison |
| `HEDGE_PROFIT_FWD` | `USD_FWD − USD_NO_HEDGE` | Forward value versus no hedge |
| `HEDGE_PROFIT_MM` | `USD_MM − USD_NO_HEDGE` | Money-market value versus no hedge |
| `HEDGE_PROFIT_PUT` | `USD_PUT − USD_NO_HEDGE` | Option value versus no hedge |

The Sensitivity table must also identify the highest-proceeds strategy at each `S_T` scenario and the best active hedge excluding the no-hedge strategy.

---

## 9. Model Review and Known Risks

The primary design risk is inconsistent use of hard-coded cell references. Phase 3 must use the standardized named ranges throughout the workbook.

A second risk is inconsistent treatment of the money-market hedge. The three steps must remain visible so that an auditor can verify the foreign borrowing, spot conversion, and USD investment independently.

A third risk is incorrect option-premium treatment. The premium must be treated as an upfront USD cash outflow and carried forward to settlement for comparison with settlement-date proceeds.

A fourth risk is incorrectly treating the forward and money-market hedges as dependent on `S_T`. Both are locked strategies and therefore must remain constant across the sensitivity grid.

---

## 10. Limitations and Next Steps

This specification intentionally uses indicative scenario and placeholder market inputs. The assigned 1.0935 forward rate and option premiums are scenario inputs, while spot, interest rates, and strikes must be replaced or confirmed with live, sourced market data in Stage 4.

Stage 3 will use this specification as the AI build prompt. The resulting workbook will be manually audited against the validation rules above. Stage 4 will replace placeholder market inputs with live, timestamped data. Stage 5 will independently validate the workbook and compare the economic evidence before a final hedge recommendation is made.

The model is therefore designed to produce evidence for a decision rather than to predetermine the hedge choice.

---

## Change Log

| Version | Date | Change |
|---|---|---|
| 1.0 | 2026-08-08 | Initial Stage 2 specification drafted from Stage 1 memo and Scenario 4 requirements; calculation flow, named ranges, sensitivity design, and validation rules specified. |