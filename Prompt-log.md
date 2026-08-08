
## Phase 2 — FX Hedging Model Specification

**Date:** 2026-08-08  
**Project:** FIN 321 International Business Finance — FX Hedging Project  
**Scenario:** U.S. Aerospace Manufacturer (`aerospace`)  
**Stage:** Phase 2 — Model Specification

### Prompt used for AI draft

I am completing Phase 2 of the FIN 321 International Business Finance FX Hedging Project. My assigned scenario is a U.S. Aerospace Manufacturer with a EUR 20,000,000 receivable settling one year from inception. The assigned indicative forward rate is 1.0935 USD/EUR, with indicative put and call premiums of 0.019 and 0.024 USD/EUR. Write a 2–3 page technical specification for the FX hedging workbook using the professor's required named ranges: FC_AMT, S0_in, F0_in, R_USD, R_FC, K_PUT, K_CALL, PREM_PUT, PREM_CALL, and T_DAYS.

The specification must include the problem statement, complete named-range input contract, tab architecture, assumptions and constraints, forward hedge calculation, three-step money-market hedge, covered-interest-parity check, put-option calculation, sensitivity plan from 0.95×S0_in to 1.05×S0_in in 1% steps, validation rules, outputs, model-review risks, and Phase 3–5 next steps. Scenario market values must be treated as indicative placeholders and replaced with live market data in Phase 4.

### AI draft

The AI produced the initial technical specification containing the required workbook architecture, named-range contract, calculation logic, sensitivity plan, validation rules, and outputs.

### Human review — gap identified

The initial draft needed clearer separation between fixed scenario inputs and market-data placeholders. In particular, spot, interest rates, and option strikes should not be presented as final market inputs in Phase 2 because the project requires live, sourced market data to replace placeholders in Phase 4.

The draft also needed the rate basis and premium treatment to be explicit so that the Phase 3 workbook would not make assumptions that were not documented in the specification.

### Human correction

I revised the specification to:

1. Clearly identify `FC_AMT`, `F0_in`, `PREM_PUT`, `PREM_CALL`, and `T_DAYS` as assigned scenario inputs.
2. Identify `S0_in`, `R_USD`, `R_FC`, `K_PUT`, and `K_CALL` as indicative market placeholders to be replaced or confirmed with live, timestamped data in Phase 4.
3. Specify ACT/360 as the interest-rate day-count convention.
4. Require the money-market hedge to be shown as three separate steps rather than one combined formula.
5. Add a covered-interest-parity validation comparing `F_IMPLIED` with `F0_in` and money-market proceeds with forward proceeds.
6. Specify that option premiums are upfront USD costs carried forward to settlement using the USD interest rate.
7. Add explicit validation checks for option continuity, the put floor, formula integrity, spreadsheet errors, and the sensitivity grid.

### Final result

The final specification was reviewed by the student and saved as:

`docs/specs/2026-08-08-Wilson-aerospace-spec.md`

The specification is intended to serve as the build instruction for Phase 3, where the workbook will be generated and then audited against the validation rules defined in this document.