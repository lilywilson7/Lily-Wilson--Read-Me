# FIN 321 FX Hedging — Market Data Memo

**Student:** Lily Wilson  
**Phase:** 4 — Live Market Data  
**Retrieval date:** August 14, 2026  
**Retrieval timestamp:** August 14, 2026, approximately 10:12 PM HST  
**Currency pair:** EUR/USD  
**Settlement horizon:** 365 days

## Market-data inputs

| Input | Value | Unit | Source / basis | Observation date | Retrieval / provenance note |
|---|---:|---|---|---|---|
| FC_AMT | 20,000,000 | EUR | Assigned scenario transaction assumption | N/A | Scenario input; not market data |
| S0_in | 1.1567 | USD/EUR | European Central Bank (ECB) euro foreign exchange reference rate | Aug. 14, 2026 | ECB reference rate published for Aug. 14, 2026 |
| R_USD | 0.0380 | Annual rate | Federal Reserve H.15 — U.S. Treasury bills, 1-year, secondary market | Aug. 13, 2026 | Latest H.15 observation available in the Aug. 14 release |
| R_FC | 0.02943 | Annual rate | 12-month EURIBOR, Bank of Finland EURIBOR table | Aug. 13, 2026 | EURIBOR data are published with a 24-hour delay |
| F0_in | 1.1664593904 | USD/EUR | CIP-implied forward calculated from S0_in, R_USD, R_FC, and T_DAYS | Aug. 14, 2026 | No live forward quote used; calculated using the assignment formula |
| K_PUT | 1.1567 | USD/EUR | Set at live spot, per scenario convention | Aug. 14, 2026 | At-the-money strike |
| K_CALL | 1.1567 | USD/EUR | Set at live spot, per scenario convention | Aug. 14, 2026 | At-the-money strike |
| PREM_PUT | 0.019 | USD/EUR | Scenario-given premium | N/A | Retained unchanged as instructed |
| PREM_CALL | 0.024 | USD/EUR | Scenario-given premium | N/A | Retained unchanged as instructed |
| T_DAYS | 365 | Days | Assigned transaction timing assumption | N/A | ACT/360 interest-rate accrual |

## Rate-selection rationale

### USD rate

I used the Federal Reserve H.15 1-year U.S. Treasury bill secondary-market rate rather than a shorter-term rate because the assignment requires a 1-year interest rate. The August 14, 2026 H.15 release reports the 1-year Treasury bill rate at 3.80% for August 13, 2026.

### EUR rate

I used the 12-month EURIBOR rate because the assignment requires a 1-year foreign-currency interest rate. The Bank of Finland's EURIBOR table reports 12-month EURIBOR at 2.943% for August 13, 2026. The Bank of Finland notes that EURIBOR data are published with a 24-hour delay, so the August 13 observation is the latest available observation for the August 14 analysis.

## CIP-implied forward calculation

Because a live 1-year forward quote was not used, the forward input was calculated using covered interest parity:

F0 = S0 × (1 + R_USD × T/360) / (1 + R_FC × T/360)

Substituting the live inputs:

F0 = 1.1567 × (1 + 0.0380 × 365/360) /
     (1 + 0.02943 × 365/360)

F0 = 1.1664593904 USD/EUR

Therefore:

**F0_in = 1.1664593904 USD/EUR**

The scenario's earlier indicative forward was approximately 1.1717745 USD/EUR. The live CIP-implied forward is lower because the live spot and selected interest-rate inputs differ from the original scenario placeholders.

## Option assumptions

The put and call premiums were not replaced with retail market quotes. The assignment explicitly instructs students to retain the scenario-given premiums because retail-accessible option quotes are unreliable for this exercise.

Therefore:

- Put premium = 0.019 USD/EUR
- Call premium = 0.024 USD/EUR

Both strikes were reset to the live spot of 1.1567 USD/EUR according to the scenario convention.

## Model and cross-check results

After populating the named-range inputs, the workbook recalculated successfully.

Key results:

- Forward proceeds ≈ $23,329,187.81
- Money-market hedge proceeds ≈ $23,329,187.81
- CIP implied forward = 1.1664593904 USD/EUR
- Interest-rate parity difference = 0
- Parity check = PASS

The FX Hedging Lab was also populated with the same live inputs. Its forward and money-market results matched the workbook to displayed rounding precision, and its parity check passed.

The sensitivity table and chart recalculated around the new live spot of 1.1567 USD/EUR.

## Sources

1. European Central Bank (ECB), Euro foreign exchange reference rates:
   https://www.ecb.europa.eu/stats/policy_and_exchange_rates/euro_reference_exchange_rates/html/index.en.html

2. Federal Reserve Board, H.15 Selected Interest Rates:
   https://www.federalreserve.gov/releases/h15/default.htm

3. Bank of Finland, EURIBOR rates:
   https://www.suomenpankki.fi/en/statistics/interest-rates-and-exchange-rates/euribor-rates/

## Structural/model notes

No structural formula changes were required for the live-data population. The existing named-range structure recalculated correctly after replacing the placeholder market inputs.

The forward input remained formula-driven using the CIP equation rather than being manually entered.