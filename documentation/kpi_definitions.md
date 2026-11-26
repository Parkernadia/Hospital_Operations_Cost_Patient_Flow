# KPI Definitions
## Hospital Operations, Cost & Patient Flow Intelligence

### 1. Purpose

This document defines the key performance indicators used in the Power BI dashboard, including their calculation, interpretation and relevant limitations.

### 2. Encounter KPIs

| KPI | Calculation | Interpretation |
|---|---|---|
| Total Encounters | Count of rows in FactEncounters | Number of encounter records |
| Total Patients | Distinct count of PATIENT in FactEncounters | Distinct patients represented by the selected encounters |
| Average Encounter Duration (Hours) | Average of DURATION_HOURS | Mean elapsed encounter duration |
| Median Encounter Duration (Hours) | Median of DURATION_HOURS | Middle elapsed duration, less sensitive to extreme values |
| Encounters Over 24 Hours | Count of encounters where OVER_24_HOURS is TRUE | Encounters with elapsed duration greater than 24 hours |
| Encounters Over 24 Hours (%) | Encounters Over 24 Hours ÷ Total Encounters | Share of encounters exceeding 24 hours |

### 3. Financial and Payer KPIs

| KPI | Calculation | Interpretation |
|---|---|---|
| Total Claim Cost | Sum of TOTAL_CLAIM_COST | Aggregate recorded encounter claim cost |
| Total Payer Coverage | Sum of PAYER_COVERAGE | Aggregate recorded payer coverage |
| Average Claim Cost | Average of TOTAL_CLAIM_COST | Mean claim cost per encounter |
| Zero Payer Coverage Encounters | Count of encounters where PAYER_COVERAGE = 0 | Number of encounter records with no recorded payer coverage |
| Zero Payer Coverage (%) | Zero Payer Coverage Encounters ÷ Total Encounters | Share of encounters with zero recorded payer coverage |

### 4. Procedure KPIs

| KPI | Calculation | Interpretation |
|---|---|---|
| Total Procedures | Count of rows in FactProcedures | Number of procedure records |
| Total Procedure Base Cost | Sum of BASE_COST | Aggregate recorded procedure base cost |
| Average Procedure Base Cost | Average of BASE_COST | Mean base cost per procedure record |
| Encounters with Procedures | Distinct count of ENCOUNTER in FactProcedures | Number of distinct encounters with at least one procedure |
| Procedure Utilisation (%) | Encounters with Procedures ÷ Total Encounters | Share of encounter records associated with at least one procedure |

### 5. DAX Measures

The principal DAX measures are:

```dax
Total Encounters =
COUNTROWS(fact_encounters)

Total Patients =
DISTINCTCOUNT(fact_encounters[PATIENT])

Average Encounter Duration (Hours) =
AVERAGE(fact_encounters[DURATION_HOURS])

Median Encounter Duration (Hours) =
MEDIAN(fact_encounters[DURATION_HOURS])

Encounters Over 24 Hours =
CALCULATE(
    [Total Encounters],
    fact_encounters[OVER_24_HOURS] = TRUE()
)

Encounters Over 24 Hours (%) =
DIVIDE(
    [Encounters Over 24 Hours],
    [Total Encounters],
    0
)

Total Claim Cost =
SUM(fact_encounters[TOTAL_CLAIM_COST])

Total Payer Coverage =
SUM(fact_encounters[PAYER_COVERAGE])

Average Claim Cost =
AVERAGE(fact_encounters[TOTAL_CLAIM_COST])

Zero Payer Coverage Encounters =
CALCULATE(
    [Total Encounters],
    fact_encounters[PAYER_COVERAGE] = 0
)

Zero Payer Coverage (%) =
DIVIDE(
    [Zero Payer Coverage Encounters],
    [Total Encounters],
    0
)

Total Procedures =
COUNTROWS(fact_procedures)

Total Procedure Base Cost =
SUM(fact_procedures[BASE_COST])

Average Procedure Base Cost =
AVERAGE(fact_procedures[BASE_COST])

Encounters with Procedures =
DISTINCTCOUNT(fact_procedures[ENCOUNTER])

Procedure Utilisation (%) =
DIVIDE(
    [Encounters with Procedures],
    [Total Encounters],
    0
)
```

### 6. Interpretation Notes

- All measures respond to the applicable report filters.
- Total Patients is a distinct count within the selected encounter records, not a count of unique patient visits.
- Procedure utilisation measures encounters with at least one procedure, not the proportion of individual patients receiving procedures.
- Average encounter duration is based on elapsed timestamps and is not automatically equivalent to inpatient length of stay.
- Zero payer coverage does not necessarily mean the patient is uninsured.
- Cost fields are displayed without a currency symbol because the source currency was not specified.
- Average procedure base cost and total procedure base cost are different measures and should not be used interchangeably.
- The dashboard does not establish a formal claim-cost reconciliation equation.