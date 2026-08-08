# Stage 3 — Workbook Build Audit

**Author:** Lily Wilson  
**Scenario:** U.S. Aerospace Manufacturer  
**Date:** 2026-08-08  
**Workbook:** `models/builds/2026-08-08-Wilson-aerospace-model.xlsx`

## Audit findings

### Finding 1 — Covered interest parity check
**Checked:** The money-market implied forward was compared with `F0_in` using the Phase 2 ACT/360 parity formula.

**Found:** The initial placeholder inputs produced an implied forward of 1.114955872 versus `F0_in` of 1.0935, causing the parity check to fail.

**Fixed:** The placeholder `R_FC` was changed from 3.3% to 5.3% so the placeholder rate assumptions are internally consistent with the placeholder forward. The parity difference is now 0 and the workbook displays PASS.

### Finding 2 — Named-range contract
**Checked:** All ten required named ranges were reviewed in the workbook.

**Found:** `FC_AMT`, `S0_in`, `F0_in`, `R_USD`, `R_FC`, `K_PUT`, `K_CALL`, `PREM_PUT`, `PREM_CALL`, and `T_DAYS` are present and attached to the intended input cells.

**Fixed:** No correction required.

### Finding 3 — Money-market intermediate formulas
**Checked:** The three money-market hedge steps were compared with the Phase 2 specification.

**Found:** Steps 2 and 3 originally referenced intermediate cell addresses rather than relying entirely on the named-range contract.

**Fixed:** Replaced the formulas with equivalent named-range calculations so the money-market pipeline remains visible while reducing dependence on cell addresses.

### Finding 4 — Sensitivity forward calculation
**Checked:** The Forward column of the sensitivity table was reviewed.

**Found:** The original formula referenced `Forward!$B$3`, creating a cross-sheet cell-address dependency.

**Fixed:** Replaced it with `=FC_AMT*F0_in` and filled the corrected formula through the sensitivity table.

### Finding 5 — Sensitivity money-market calculation
**Checked:** The Money Market column of the sensitivity table was reviewed.

**Found:** The original formula referenced `Money_Market!$B$5`.

**Fixed:** Replaced it with the named-range money-market calculation and filled the corrected formula through the sensitivity table.

## Final validation

- All ten required named ranges present and correctly attached.
- Forward hedge formula computes from named inputs.
- Money-market hedge contains three explicit steps.
- Covered interest parity check passes.
- Put and call option calculations include premiums.
- Sensitivity table covers 95% through 105% of `S0_in` in 1% increments.
- Sensitivity calculations are formula-driven.
- Comparison chart is present.
- No obvious spreadsheet error cells remain.