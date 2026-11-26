# Data Quality Assessment
## Hospital Operations, Cost & Patient Flow Intelligence

### 1. Overview

This assessment documents the quality checks performed on the source datasets before analytical modelling and dashboard development.

The project uses five primary datasets: encounters, patients, procedures, payers and organisations. A data dictionary was also supplied to support field interpretation.

| Dataset | Records | Columns |
|---|---:|---:|
| Encounters | 27,891 | 14 |
| Patients | 974 | 20 |
| Procedures | 47,701 | 9 |
| Payers | 10 | 7 |
| Organisations | 1 | 8 |
| Data dictionary | 65 | 3 |

The five operational datasets contain 76,577 records in total.

### 2. Completeness and Missing Values

Missing values were identified in several fields.

| Dataset | Field | Missing records | Interpretation |
|---|---|---:|---|
| Encounters | REASONCODE | 19,541 | Reason codes are not populated for many encounters |
| Encounters | REASONDESCRIPTION | 19,541 | Reason descriptions are not populated for many encounters |
| Patients | DEATHDATE | 820 | May indicate patients without a recorded death date |
| Patients | SUFFIX | 953 | Optional demographic information |
| Patients | MAIDEN | 588 | Optional demographic information |
| Patients | ZIP | 142 | Incomplete postal information |
| Patients | MARITAL | 1 | Isolated missing value |
| Procedures | REASONCODE | 36,945 | Reason codes are not populated for many procedures |
| Procedures | REASONDESCRIPTION | 36,945 | Reason descriptions are not populated for many procedures |
| Payers | ADDRESS, CITY, STATE_HEADQUARTERED, ZIP, PHONE | 1 each | Missing information belongs to one payer record |
| Data dictionary | Field | 5 | Some dictionary entries do not specify a field |

Missing values were retained where they did not prevent the intended analysis. Optional demographic fields and reason codes were not artificially populated.

The missing payer information belongs to the `NO_INSURANCE` record. This is an explicit payer category, not a missing payer identifier.

### 3. Duplicate and Key Validation

No exact duplicate rows were found in any of the six supplied datasets.

Primary key checks:

| Dataset | Primary key | Records | Unique keys | Missing keys |
|---|---|---:|---:|---:|
| Encounters | Id | 27,891 | 27,891 | 0 |
| Patients | Id | 974 | 974 | 0 |
| Payers | Id | 10 | 10 | 0 |
| Organisations | Id | 1 | 1 | 0 |

All tested primary keys were unique and non-missing.

### 4. Referential Integrity

Foreign key checks confirmed that all tested references matched their corresponding records.

| Reference | Records tested | Unmatched |
|---|---:|---:|
| Encounters → Patients | 27,891 | 0 |
| Encounters → Payers | 27,891 | 0 |
| Encounters → Organisations | 27,891 | 0 |
| Procedures → Patients | 47,701 | 0 |
| Procedures → Encounters | 47,701 | 0 |

An additional consistency check confirmed that the patient associated with each procedure matched the patient associated with its referenced encounter. No mismatches were found.

### 5. Date and Duration Validation

All non-missing date values in the tested date fields were successfully parsed.

The encounter dataset covers 2 January 2011 to 5 February 2022. Procedure records cover 2 January 2011 to 29 January 2022.

Encounter duration was calculated as the difference between the stop and start timestamps.

| Check | Encounters | Procedures |
|---|---:|---:|
| Records tested | 27,891 | 47,701 |
| Start after stop | 0 | 0 |
| Start equal to stop | 0 | 2 |
| Missing start or stop | 0 | 0 |

Two procedures had zero recorded duration. These records were retained and flagged rather than removed.

Encounter duration findings:

- Average duration: 7.27 hours.
- Median duration: 0.25 hours.
- Encounters exceeding 24 hours: 75.
- Maximum recorded duration: 44,930 hours.

The maximum duration is an extreme value and requires source-level investigation. Encounter duration is an elapsed timestamp measure and should not automatically be interpreted as verified inpatient length of stay.

Urgent care and wellness encounters also showed repeated 15-minute durations. This may reflect source-data conventions rather than actual service duration.

### 6. Financial Data Validation

The three financial fields in the encounter dataset had no missing or negative values.

| Field | Mean | Minimum | Maximum |
|---|---:|---:|---:|
| BASE_ENCOUNTER_COST | 116.18 | 85.55 | 146.18 |
| TOTAL_CLAIM_COST | 3,639.68 | 0.00 | 641,882.70 |
| PAYER_COVERAGE | 1,114.97 | 0.00 | 247,751.42 |

Additional checks:

- 89 encounters had a total claim cost of zero.
- 13,586 encounters had zero payer coverage.
- No encounters had payer coverage greater than total claim cost.
- No negative values were found in the three financial fields.

Zero payer coverage was not treated as proof that a patient was uninsured. The `NO_INSURANCE` payer category was kept distinct from other payer records.

The source data did not specify a currency. Financial measures are therefore displayed without a currency symbol.

### 7. Procedure Data Validation

The procedures dataset contains 47,701 records.

- No missing procedure base costs were identified.
- No zero or negative procedure base costs were identified.
- Two procedures had zero recorded duration.
- There were 157 unique procedure codes and 163 unique descriptions.
- Four procedure codes were associated with multiple descriptions.
- No description was associated with multiple codes.

The code-description inconsistencies were retained as source-data standardisation issues. Both code and description were preserved in the analytical model.

### 8. Procedure and Encounter Cost Comparison

Procedure costs were aggregated by encounter for analytical comparison.

The comparison identified differences between total claim cost and the combined encounter and procedure base costs.

This comparison is descriptive only. The source data does not establish that total claim cost must equal encounter base cost plus procedure base costs. Differences were therefore not automatically classified as errors, losses or revenue.

### 9. Data Preparation Decisions

The following decisions guided preparation:

1. Retained source records rather than deleting unusual values without supporting evidence.
2. Preserved missing optional fields instead of creating unsupported replacements.
3. Flagged zero-duration procedures and encounters exceeding 24 hours.
4. Created separate encounter and procedure fact tables to preserve their different levels of detail.
5. Retained explicit payer categories, including `NO_INSURANCE`.
6. Preserved procedure codes and descriptions to support investigation of inconsistent mappings.
7. Excluded patient demographic details from the analytical model, retaining only the patient reference key.

### 10. Conclusion

The source datasets passed the tested primary key, foreign key, duplicate and date parsing checks. The expected record counts were preserved during preparation.

Several limitations remain, particularly extreme encounter durations, incomplete optional fields, procedure code-description inconsistencies and the absence of a documented currency.

The prepared datasets were considered suitable for the defined operational, financial and procedure analyses, subject to the interpretation limitations documented above.