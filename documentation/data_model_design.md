# Data Model Design

## Hospital Operations, Cost & Patient Flow Intelligence

### 1. Overview

A dimensional data model was developed to support encounter, financial, payer and procedure analysis in Power BI.

The model separates encounter-level records from procedure-level records to preserve their respective grains and prevent duplication when analysing costs and activity.

### 2. Fact Tables

#### FactEncounters

**Grain:** One row per encounter.

| Attribute | Description |
| --- | --- |
| Id | Unique encounter identifier |
| PATIENT | Patient reference key |
| PAYER | Payer reference key |
| ORGANIZATION | Organisation reference key |
| ENCOUNTERCLASS | Encounter classification |
| START / STOP | Encounter timestamps |
| BASE_ENCOUNTER_COST | Encounter base cost |
| TOTAL_CLAIM_COST | Total claim cost |
| PAYER_COVERAGE | Recorded payer coverage |
| DURATION_HOURS | Derived elapsed encounter duration |
| START_DATE / STOP_DATE | Derived date fields |
| OVER_24_HOURS | Flag for duration exceeding 24 hours |

**Record count:** 27,891.

#### FactProcedures

**Grain:** One row per procedure.

| Attribute | Description |
| --- | --- |
| PROCEDURE_ID | Generated unique procedure row identifier |
| PATIENT | Patient reference key |
| ENCOUNTER | Related encounter identifier |
| PAYER | Payer inherited from the related encounter |
| ORGANIZATION | Organisation inherited from the related encounter |
| ENCOUNTERCLASS | Encounter class inherited from the related encounter |
| START / STOP | Procedure timestamps |
| CODE | Procedure code |
| DESCRIPTION | Procedure description |
| BASE_COST | Procedure base cost |
| DURATION_HOURS | Derived elapsed procedure duration |
| PROCEDURE_DATE | Derived procedure date |
| ENCOUNTER_DATE | Related encounter date |
| ZERO_DURATION | Flag for zero-duration procedures |

**Record count:** 47,701.

`PROCEDURE_ID` was generated to uniquely identify procedure rows because the source procedure dataset did not provide a separate unique procedure identifier.

### 3. Dimension Tables

| Dimension | Key | Records | Purpose |
| --- | --- | ---: | --- |
| DimPatients | PATIENT | 974 | Patient reference and distinct patient counting |
| DimPayers | PAYER | 10 | Payer identification and grouping |
| DimOrganizations | ORGANIZATION | 1 | Organisation reference |
| DimEncounterClass | ENCOUNTERCLASS | 6 | Encounter classification |
| DimDate | DATE | 4,053 | Calendar analysis and date filtering |

The patient dimension contains only the patient reference key. Patient demographic and contact details were excluded from the analytical model.

The date dimension covers 2 January 2011 to 5 February 2022 and includes year, quarter, month, year-month, day and day-name attributes.

### 4. Relationship Design

The model uses one-to-many relationships from dimensions to fact tables.

| One side | Many side | Relationship |
| --- | --- | --- |
| DimPatients | FactEncounters | PATIENT |
| DimPatients | FactProcedures | PATIENT |
| DimPayers | FactEncounters | PAYER |
| DimPayers | FactProcedures | PAYER |
| DimOrganizations | FactEncounters | ORGANIZATION |
| DimOrganizations | FactProcedures | ORGANIZATION |
| DimEncounterClass | FactEncounters | ENCOUNTERCLASS |
| DimEncounterClass | FactProcedures | ENCOUNTERCLASS |
| DimDate | FactEncounters | DATE to START_DATE |
| DimDate | FactProcedures | DATE to PROCEDURE_DATE |

All relationships are active and use single-direction filtering from dimensions to fact tables.

No direct relationship was created between FactEncounters and FactProcedures.

### 5. Modelling Decisions

**Separate fact tables*

Encounters and procedures have different levels of detail. Combining procedure rows directly with encounter rows would repeat encounter-level financial values for encounters containing multiple procedures.

Keeping the fact tables separate avoids this duplication.

**Inherited procedure attributes*

Payer, organisation and encounter class are inherited by each procedure from its associated encounter. This enables shared dimension filtering without creating a direct fact-to-fact relationship.

**Date modelling*

The date dimension filters encounters by encounter start date and procedures by procedure date.

**Derived duration**  

Duration is calculated from the difference between stop and start timestamps. Original timestamps are retained.

**Financial interpretation**  

The model does not assume that total claim cost equals encounter base cost plus procedure base costs. These fields are analysed separately.

### 6. Validation

| Validation | Result |
|------|
| FactEncounters record count | 27,891 — PASS |
| FactProcedures record count | 47,701 — PASS |
| FactEncounters unique keys | 27,891 — PASS |
| FactProcedures unique keys | 47,701 — PASS |
| Missing fact keys | 0 — PASS |
| Tested dimension references | 0 unmatched — PASS |
| Procedure-to-encounter references | 0 unmatched — PASS |
| Procedure patient consistency | 0 mismatches — PASS |
| Encounter dates outside date dimension | 0 |
| Procedure dates outside date dimension | 0 |

### 7. Exported Analytical Tables

The following seven prepared CSV files were exported to `data/processed/`:

| File | Records | Columns |
| --- | ---: | ---: |
| fact_encounters.csv | 27,891 | 14 |
| fact_procedures.csv | 47,701 | 15 |
| dim_patients.csv | 974 | 1 |
| dim_payers.csv | 10 | 2 |
| dim_organizations.csv | 1 | 4 |
| dim_encounter_class.csv | 6 | 1 |
| dim_date.csv | 4,053 | 8 |

### 8. Conclusion

The dimensional model preserves the encounter and procedure grains, supports shared filtering and passed the documented relationship and record-count checks.

It provides the analytical foundation for the Power BI dashboard and its operational, financial and procedure utilisation measures.
