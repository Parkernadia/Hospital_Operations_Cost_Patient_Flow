# Hospital Operations, Cost & Patient Flow Intelligence

## Analytical Data Model Design

### 1. Overview

The analytical data model was developed to support the investigation of hospital encounter volumes, patient flow, encounter costs, payer coverage and procedure utilisation.

The model uses a dimensional modelling approach consisting of two fact tables and five dimension tables.

The design separates encounter-level and procedure-level information to preserve the correct level of detail and prevent the duplication of encounter-level financial values when analysing procedures.

### 2. Fact Tables

| Fact table | Grain | Records |
| --- | --- | ---: |
| FactEncounters | One row per encounter | 27,891 |
| FactProcedures | One row per procedure | 47,701 |

**FactEncounters*

Contains encounter-level information, including:

- Encounter identifier
- Patient, payer and organisation references
- Encounter class
- Start and stop timestamps
- Base encounter cost
- Total claim cost
- Payer coverage
- Derived encounter duration
- Encounter start and stop dates
- Indicator for encounters exceeding 24 hours

**FactProcedures*

Contains procedure-level information, including:

- Generated procedure row identifier
- Patient and encounter references
- Procedure start and stop timestamps
- Procedure code and description
- Procedure base cost
- Derived procedure duration
- Procedure date
- Zero-duration indicator
- Payer, organisation and encounter class inherited from the associated encounter
- Encounter date

The generated `PROCEDURE_ID` provides a unique row identifier for the prepared table. It is not an original identifier supplied by the source dataset.

### 3. Dimension Tables

| Dimension | Records | Purpose |
| --- | ---: | --- |
| DimPatients | 974 | Supports analysis by patient reference |
| DimPayers | 10 | Supports analysis by payer |
| DimOrganizations | 1 | Supports organisation-level analysis |
| DimEncounterClass | 6 | Supports comparison across encounter classes |
| DimDate | 4,053 | Supports daily, monthly, quarterly and yearly analysis |

The patient dimension contains only the patient reference key. Direct patient-identifying information was excluded from the analytical model to minimise unnecessary exposure of personal information.

The date dimension covers 2 January 2011 to 5 February 2022 and includes year, quarter, month, year-month, day and day-name attributes.

### 4. Relationship Design

Each dimension is designed to have a one-to-many relationship with the relevant fact tables.

| Dimension key | Fact table | Corresponding foreign key |
| --- | --- | --- |
| DimPatients[PATIENT] | FactEncounters | PATIENT |
| DimPatients[PATIENT] | FactProcedures | PATIENT |
| DimPayers[PAYER] | FactEncounters | PAYER |
| DimPayers[PAYER] | FactProcedures | PAYER |
| DimOrganizations[ORGANIZATION] | FactEncounters | ORGANIZATION |
| DimOrganizations[ORGANIZATION] | FactProcedures | ORGANIZATION |
| DimEncounterClass[ENCOUNTERCLASS] | FactEncounters | ENCOUNTERCLASS |
| DimEncounterClass[ENCOUNTERCLASS] | FactProcedures | ENCOUNTERCLASS |
| DimDate[DATE] | FactEncounters | START_DATE |
| DimDate[DATE] | FactProcedures | PROCEDURE_DATE |

The intended Power BI relationships use single-direction filtering from dimensions to fact tables.

No direct relationship is created between FactEncounters and FactProcedures. Shared dimensions provide the common analytical context.

### 5. Modelling Decisions

**Separate fact tables*

Encounter and procedure records have different levels of detail. Keeping them separate prevents encounter-level costs from being duplicated when an encounter has multiple procedures.

**Inherited encounter attributes*

Payer, organisation and encounter class are carried into FactProcedures through a validated many-to-one merge with FactEncounters. This allows procedure analysis to use the same analytical dimensions.

**Date attributes*

Encounter analysis uses the encounter start date, while procedure analysis uses the procedure start date. Original timestamps are retained for more detailed analysis.

**Data quality exceptions*

Long encounter durations and zero-duration procedures are retained and flagged rather than automatically deleted. These records require careful interpretation.

**Financial interpretation*

Total claim cost is not assumed to equal the sum of encounter base cost and procedure base costs. Differences are treated as reconciliation observations rather than confirmed errors.

### 6. Model Validation

The prepared model was subjected to integrity checks before export.

| Validation | Result |
| --- | --- |
| FactEncounters row count | 27,891 — passed |
| FactProcedures row count | 47,701 — passed |
| FactEncounters unique primary keys | 27,891 — passed |
| FactProcedures unique primary keys | 47,701 — passed |
| Missing fact primary keys | 0 |
| Unmatched fact-to-dimension references | 0 across eight checks |
| Unmatched procedure-to-encounter references | 0 |
| Procedure-to-encounter patient mismatches | 0 |
| Encounter dates outside calendar | 0 |
| Procedure dates outside calendar | 0 |

All tested relationship and integrity checks passed.

### 7. Exported Analytical Tables

Seven CSV files were exported to `data/processed/`:

| File | Records | Columns |
| --- | ---: | ---: |
| fact_encounters.csv | 27,891 | 14 |
| fact_procedures.csv | 47,701 | 15 |
| dim_patients.csv | 974 | 1 |
| dim_payers.csv | 10 | 2 |
| dim_organizations.csv | 1 | 4 |
| dim_encounter_class.csv | 6 | 1 |
| dim_date.csv | 4,053 | 8 |

These files form the prepared analytical dataset for the Power BI dashboard.

### 8. Conclusion

The analytical data model has been constructed and validated. It preserves the original encounter and procedure record counts, maintains the tested relationships and provides a structured foundation for analysing hospital operations, costs, payer coverage and procedure utilisation.

The next stage is to implement the model in Power BI and develop the interactive dashboard.
