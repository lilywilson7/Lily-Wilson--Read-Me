# FX Hedging Stage 5 — Validation

**Student:** Lily Wilson  
**Course:** FIN 321 — International Business Finance  
**Date:** August 14, 2026  
**Scenario:** U.S. aerospace manufacturer with EUR receivable

## 1. Independent LLM Execution

### Documents provided

The independent LLM run was conducted in a fresh Claude conversation using exactly two project documents:

1. Stage 2 specification
2. Stage 4 market-data memo

No workbook, prior calculations, screenshots, or previous ChatGPT conversation was provided to Claude.

The raw Claude response is preserved separately here:

`analysis/2026-08-14-Wilson-llm-output.md`

### Prompt used

> You are completing an independent production test of an FX hedging model.
>
> Use ONLY the two documents provided in this conversation: (1) the Stage 2 specification and (2) the Stage 4 market-data memo.
>
> Do not assume access to any other project files, workbook results, prior conversations, or external information.
>
> Independently compute the complete FX hedge analysis specified by the documents, including:
>
> 1. The unhedged outcome across the required settlement-spot scenarios.
> 2. The forward hedge outcome.
> 3. The three-step money-market hedge.
> 4. The put hedge, including premium.
> 5. The call participation strategy, including premium.
> 6. The required sensitivity analysis across settlement spot rates.
> 7. Any parity or consistency checks required by the specification.
>
> Show your formulas, assumptions, intermediate calculations, and final numerical results clearly. Use the named inputs and conventions specified in the documents.
>
> Then recommend the strategy you believe is most appropriate based solely on the analysis and the firm's stated exposure/risk considerations in the documents.
>
> Do not attempt to match an expected answer. Do not infer or use results from an unseen workbook. If the documents leave something ambiguous, explicitly identify the ambiguity and state the assumption you make.
>
> This is an independent analysis, so provide your complete answer in one response.

## 2. Independent LLM Results

Claude independently used the following inputs:

| Named range | Value |
|---|---:|
| FC_AMT | €20,000,000 |
| S0_in | 1.1567 USD/EUR |
| R_USD | 3.80% |
| R_FC | 2.943% |
| F0_in | 1.1664593904 USD/EUR |
| K_PUT | 1.1567 USD/EUR |
| K_CALL | 1.1567 USD/EUR |
| PREM_PUT | 0.019 USD/EUR |
| PREM_CALL | 0.024 USD/EUR |
| T_DAYS | 365 |
| BASIS | 360 |

Claude calculated the forward proceeds as $23,329,187.81 and the money-market hedge as the same amount. Its three money-market steps were €19,420,516.08 borrowed, $22,463,710.95 converted at spot, and $23,329,187.81 after USD investment. These results agree with the workbook. Claude also identified that the forward and money-market results converge under covered interest-rate parity. 

## 3. Comparison With Workbook

### 95% settlement spot

| Strategy | Claude | Workbook | Difference | Diagnosis |
|---|---:|---:|---:|---|
| Unhedged | $21,977,300.00 | $21,977,300.00 | $0.00 | Match |
| Forward | $23,329,187.81 | $23,329,187.81 | $0.00 | Match |
| Money market | $23,329,187.81 | $23,329,187.81 | $0.00 | Match |
| Put | $22,739,359.44 | $22,754,000.00 | -$14,640.56 | LLM premium treatment |
| Call | Not applied to receivable | -$480,000.00 | N/A | Spec ambiguity |

### 100% settlement spot

| Strategy | Claude | Workbook | Difference | Diagnosis |
|---|---:|---:|---:|---|
| Unhedged | $23,134,000.00 | $23,134,000.00 | $0.00 | Match |
| Forward | $23,329,187.81 | $23,329,187.81 | $0.00 | Match |
| Money market | $23,329,187.81 | $23,329,187.81 | $0.00 | Match |
| Put | $22,739,359.44 | $22,754,000.00 | -$14,640.56 | LLM premium treatment |
| Call | Not applied to receivable | -$480,000.00 | N/A | Spec ambiguity |

### 105% settlement spot

| Strategy | Claude | Workbook | Difference | Diagnosis |
|---|---:|---:|---:|---|
| Unhedged | $24,290,700.00 | $24,290,700.00 | $0.00 | Match |
| Forward | $23,329,187.81 | $23,329,187.81 | $0.00 | Match |
| Money market | $23,329,187.81 | $23,329,187.81 | $0.00 | Match |
| Put | $23,896,059.44 | $23,910,700.00 | -$14,640.56 | LLM premium treatment |
| Call | Not applied to receivable | $676,700.00 | N/A | Spec ambiguity |

## 4. Discrepancy Diagnosis

### Put premium

The primary numerical discrepancy was the put premium.

The workbook treats the scenario-given premium of $0.019/EUR as a direct premium:

`$0.019 × €20,000,000 = $380,000`

Claude instead future-valued the premium using the USD accumulation factor and calculated a settlement cost of $394,640.56. Claude explicitly used:

`FV_PREM_PUT = -PREM_PUT × FC_AMT × DF_USD`

The resulting difference was $14,640.56. Claude's approach therefore does not match the workbook's treatment of the scenario-given premium.

This is diagnosed as an LLM calculation/interpretation error rather than a workbook error.

### Call strategy

Claude did not calculate a comparable call-receivable result. It interpreted the call inputs as belonging to the future-payable variant and stated that the documents did not clearly define a call-based participation strategy for the current EUR receivable.

This is useful evidence of a specification ambiguity. Rather than inventing a receivable call structure, Claude explicitly stated its assumption.

## 5. Hand Verification

### A. Forward hedge

Using the named-range notation:

`FC_AMT × F0_in`

`€20,000,000 × 1.1664593904`

`= $23,329,187.81`

This reconciles to the workbook forward result.

### B. Money-market hedge

#### Step 1 — Borrow EUR

`FC_AMT / (1 + R_FC × T_DAYS/360)`

`20,000,000 / (1 + 0.02943 × 365/360)`

`= €19,420,516.08`

#### Step 2 — Convert to USD

`Borrow_FC × S0_in`

`€19,420,516.08 × 1.1567`

`= $22,463,710.95`

#### Step 3 — Invest USD

`USD_NOW × (1 + R_USD × T_DAYS/360)`

`$22,463,710.95 × (1 + 0.038 × 365/360)`

`= $23,329,187.81`

The money-market hedge therefore reconciles to the forward hedge.

### C. Put hedge at S_T = S0

`MAX(S_T, K_PUT) × FC_AMT - PREM_PUT × FC_AMT`

`MAX(1.1567, 1.1567) × 20,000,000 - 0.019 × 20,000,000`

`= $23,134,000 - $380,000`

`= $22,754,000`

This reconciles to the workbook.

## 6. Key Validation Findings

The forward hedge and money-market hedge independently reconcile to the same locked proceeds of $23,329,187.81.

The unhedged outcome varies directly with the settlement spot rate.

The put provides a floor while retaining upside participation above the strike, but the premium reduces proceeds.

The independent Claude analysis reproduced the core forward, money-market, and unhedged calculations but treated the option premium differently.

The call strategy exposed an ambiguity in how the specification presents call participation for the current EUR receivable.

## 7. Spec Retrospective

The independent run showed that the specification was sufficient for the LLM to reproduce the core FX calculations, but it was not completely unambiguous about option-premium timing and the role of the call strategy.

The clearest issue was the treatment of the put premium. The market-data memo retained the scenario-given premium, while Claude interpreted the premium as a cash cost that should be future-valued to settlement. The specification should explicitly state whether option premiums are reported as a payment today or as a settlement-date-equivalent cost. A v2 specification should state the exact formula and timing convention for option premiums.

The call inputs also caused uncertainty. Claude interpreted the call as a payable-side instrument because the specification describes it as a future payable variant and says it is not the primary hedge for the EUR receivable. A v2 specification should explicitly state whether the call should be calculated in the sensitivity table for the current receivable, and if so, provide the exact payoff direction and formula.

These results show that the specification was sufficient for the main forward and money-market calculations but could be more explicit about option timing and the intended treatment of the call strategy.