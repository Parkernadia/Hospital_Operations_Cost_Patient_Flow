# Dashboard Validation

## Hospital Operations, Cost & Patient Flow Intelligence

### 1. Purpose

This document records the validation performed on the Power BI dashboard, including measure reconciliation, report filtering and visual checks.

### 2. Dashboard Pages

The report contains five pages:

1. Executive Overview
2. Encounter Analysis
3. Financial & Payer Analysis
4. Procedure Utilisation
5. Data & Methodology

### 3. Independent Measure Validation

The following Power BI measures were compared with independently calculated Python results.

| Measure | Expected result | Validation |
| --- | ---: | --- |
| Total Encounters | 27,891 | PASS |
| Total Patients | 974 | PASS |
| Average Encounter Duration (Hours) | 7.27 | PASS |
| Median Encounter Duration (Hours) | 0.25 | PASS |
| Encounters Over 24 Hours | 75 | PASS |
| Encounters Over 24 Hours (%) | 0.27% | PASS |
| Total Claim Cost | 101,514,375.52 | PASS |
| Total Payer Coverage | 31,097,506.99 | PASS |
| Zero Payer Coverage Encounters | 13,586 | PASS |
| Total Procedures | 47,701 | PASS |
| Average Procedure Base Cost | 2,212.06 | PASS |
| Encounters with Procedures | 14,670 | PASS |
| Procedure Utilisation (%) | 52.60% | PASS |

The displayed values were checked against Python calculations. The figures above are the unfiltered overall results.

### 4. Page-Level Filter Validation

| Dashboard page | Validation performed | Status |
| --- | --- | --- |
| Executive Overview | Year, encounter class and payer slicers; visual response | PASS |
| Encounter Analysis | Year and encounter class slicers; duration calculations | PASS |
| Financial & Payer Analysis | Year, encounter class and payer slicers | Pending final confirmation |
| Procedure Utilisation | Year, encounter class and payer slicers | Pending final confirmation |
| Data & Methodology | Content and interpretation review | Completed |

### 5. Encounter Analysis Checks

The overall encounter figures were confirmed:

- Total encounters: 27,891.
- Average duration: 7.27 hours.
- Encounters exceeding 24 hours: 75.
- Inpatient encounters exceeding 24 hours: 58.

### 6. Financial and Payer Checks

Expected overall figures:

- Total claim cost: 101,514,375.52.
- Total payer coverage: 31,097,506.99.
- Zero payer coverage encounters: 13,586.

The numerical reconciliation and final slicer checks should be marked complete only after they have been confirmed in Power BI.

### 7. Procedure Checks

Expected overall figures:

- Total procedures: 47,701.
- Encounters with procedures: 14,670.
- Procedure utilisation: 52.60%.
- Average procedure base cost: 2,212.06.

The procedure page's final filter and visual checks should be marked complete only after confirmation in Power BI.

### 8. Model and Data Validation

The analytical model passed the documented checks:

- Both fact tables retained their expected row counts.
- Fact table keys were unique and non-missing.
- Tested foreign key relationships had no unmatched records.
- Procedure-to-encounter patient consistency had no mismatches.
- Encounter and procedure dates fell within the date dimension.
- No direct fact-to-fact relationship was created.

### 9. Interpretation and Presentation Checks

The following presentation rules were applied:

- Financial measures have no unsupported currency symbol.
- Percentage measures are formatted as percentages.
- Record counts are displayed as whole numbers.
- Patient demographic and contact details are excluded from the analytical model.
- Zero payer coverage is not presented as equivalent to being uninsured.
- Average procedure cost is distinguished from total procedure cost.
- Encounter duration is not automatically interpreted as inpatient length of stay.

### 10. Outstanding Validation

Before final submission, confirm:

1. Financial & Payer Analysis slicers and overall figures.
2. Procedure Utilisation slicers and overall figures.
3. Top 10 procedure visual filters.
4. Chronological month sorting.
5. Full report readability and absence of patient-identifiable information.

### 11. Conclusion

The analytical model and principal DAX measures were independently validated against Python calculations. The Executive Overview and Encounter Analysis pages passed their recorded filter checks.

The remaining page-level checks should be completed and documented before the dashboard is treated as fully validated for submission.
