# Hospital Operations, Cost & Patient Flow Intelligence

## Data Quality Assessment

### 1. Overview

The purpose of this assessment was to evaluate the completeness, consistency, validity and structural integrity of the datasets supplied for the hospital operations analytics project.

Six datasets were examined: encounters, patients, procedures, payers, organizations and the data dictionary.

The assessment focused on dataset structure, missing values, duplicate records, key uniqueness, referential integrity, date validity, encounter duration, financial data and consistency between related datasets.

The findings inform subsequent data preparation, analytical modelling and dashboard development.

### 2. Dataset Overview

The six datasets contain 76,642 records across 61 columns.

| Dataset | Records | Columns |
| --- | ---: | ---: |
| Encounters | 27,891 | 14 |
| Patients | 974 | 20 |
| Procedures | 47,701 | 9 |
| Payers | 10 | 7 |
| Organizations | 1 | 8 |
| Data Dictionary | 65 | 3 |
| **Total** | **76,642** | **61** |

The encounters dataset contains the primary records for analysing hospital activity. Patients, payers and organizations provide associated reference information, while procedures contain services linked to individual encounters.

### 3. Completeness Assessment

Missing values were identified in several fields.

| Dataset | Field | Missing Values | Percentage |
| --- | --- | ---: | ---: |
| Encounters | REASONCODE | 19,541 | 70.06% |
| Encounters | REASONDESCRIPTION | 19,541 | 70.06% |
| Patients | SUFFIX | 953 | 97.84% |
| Patients | DEATHDATE | 820 | 84.19% |
| Patients | MAIDEN | 588 | 60.37% |
| Patients | ZIP | 142 | 14.58% |
| Patients | MARITAL | 1 | 0.10% |
| Procedures | REASONCODE | 36,945 | 77.45% |
| Procedures | REASONDESCRIPTION | 36,945 | 77.45% |
| Payers | ADDRESS, CITY, STATE_HEADQUARTERED, ZIP, PHONE | 1 each | 10.00% |

The data dictionary also contains five missing values in its Field column.

Missing reason codes and descriptions may reflect optional clinical information. Missing patient death dates may indicate that a patient was alive at the time represented in the dataset. Missing suffix and maiden-name fields may also be expected where these details are not applicable or recorded.

The five missing payer attributes belong to the same payer record, identified as NO_INSURANCE. These missing descriptive attributes do not represent five missing payer records.

Missing values will not be automatically imputed or removed. Their treatment will depend on their relevance to each analytical question.

### 4. Duplicate and Key Integrity Assessment

No exact duplicate rows were identified in any of the six datasets.

Primary key validation was also completed.

| Dataset | Primary Key | Records | Unique Keys | Missing Keys |
| --- | --- | ---: | ---: | ---: |
| Encounters | Id | 27,891 | 27,891 | 0 |
| Patients | Id | 974 | 974 | 0 |
| Payers | Id | 10 | 10 | 0 |
| Organizations | Id | 1 | 1 | 0 |

All tested primary keys were unique and complete.

Foreign key validation produced the following results:

| Relationship | Records Tested | Unmatched References |
| --- | ---: | ---: |
| Encounters → Patients | 27,891 | 0 |
| Encounters → Payers | 27,891 | 0 |
| Encounters → Organizations | 27,891 | 0 |
| Procedures → Patients | 47,701 | 0 |
| Procedures → Encounters | 47,701 | 0 |

An additional consistency check confirmed that every procedure's patient reference matched the patient associated with its linked encounter. No mismatches were identified.

These results support the structural integrity of the tested relationships.

### 5. Date and Duration Validation

All non-missing date values in the selected date fields were successfully parsed. No unparseable date values were identified.

The encounter and procedure datasets were checked for chronological inconsistencies.

| Validation | Encounters | Procedures |
| --- | ---: | ---: |
| Total records | 27,891 | 47,701 |
| Missing start or stop dates | 0 | 0 |
| Start after stop | 0 | 0 |
| Start equal to stop | 0 | 2 |
| Positive duration | 27,891 | 47,699 |

Two procedure records have identical start and stop timestamps. These records will be retained but flagged for consideration during analysis.

Encounter duration was calculated as the difference between STOP and START.

The average encounter duration was 7.27 hours, while the median was 0.25 hours. Seventy-five encounters exceeded 24 hours, representing approximately 0.27% of all encounters.

The longest recorded duration was 44,930 hours. Several unusually long durations were observed, including records classified as ambulatory and outpatient.

These values require cautious interpretation. The calculated duration represents elapsed time between recorded timestamps and cannot automatically be treated as actual patient length of stay. The unusually long durations will be retained and investigated rather than deleted or capped.

### 6. Financial Data Assessment

The following encounter-level financial fields were assessed:

- BASE_ENCOUNTER_COST
- TOTAL_CLAIM_COST
- PAYER_COVERAGE

All 27,891 encounter records contained values in these three fields.

| Financial Check | Result |
| --- | ---: |
| Missing financial values | 0 |
| Negative total claim costs | 0 |
| Zero total claim costs | 89 |
| Negative payer coverage values | 0 |
| Zero payer coverage values | 13,586 |
| Payer coverage exceeding total claim cost | 0 |

The total claim cost was 101,514,375.52, while total payer coverage was 31,097,506.99.

The aggregate recorded payer coverage ratio was approximately 30.63%.

Zero claim costs and zero payer coverage values were retained because they may represent meaningful records. They were not automatically classified as missing or invalid.

The difference between total claim costs and payer coverage should not automatically be interpreted as patient out-of-pocket expenditure or unpaid hospital debt.

### 7. Procedure Data Assessment

The procedures dataset contains 47,701 records, representing procedures linked to individual encounters.

Of the 27,891 encounters:

- 14,670 had at least one linked procedure.
- 13,221 had no linked procedure.
- 52.60% had at least one linked procedure.

Procedure base costs contained no missing, zero or negative values.

The mean procedure base cost was 2,212.06, while the median was 431.00. The maximum recorded procedure base cost was 289,531.00, indicating a strongly right-skewed distribution.

A code-to-description consistency check identified four procedure codes associated with multiple descriptions. No description was associated with multiple procedure codes.

These inconsistencies will be documented and investigated during preparation. Procedure codes will be retained as the primary grouping field where appropriate, while original descriptions will be preserved.

The combined encounter and procedure base costs were also compared with total claim costs. Differences were identified, but no documented rule establishes that total claim cost must equal the sum of these base costs. These differences will therefore be treated as reconciliation observations rather than confirmed errors.

### 8. Payer Data Assessment

All 27,891 encounter records matched to a payer record.

The dataset contains ten payer categories, including an explicit NO_INSURANCE category.

NO_INSURANCE accounts for 8,807 encounters, representing approximately 31.58% of all encounters. All these records have zero recorded payer coverage.

Other payer categories show considerable variation in recorded coverage. Medicare and Medicaid have positive coverage recorded for more than 99% of their encounters. In contrast, several commercial payer categories have positive coverage recorded for fewer than 9% of their encounters.

Anthem has no positive coverage recorded across its 704 encounters.

These patterns are retained for analysis, but the dataset does not explain why coverage is absent in particular records. The findings should not be interpreted as definitive measures of insurer performance.

Payer-level aggregation was reconciled against the original encounter dataset:

| Measure | Original Dataset | Payer Aggregation |
| --- | ---: | ---: |
| Encounter count | 27,891 | 27,891 |
| Total claim cost | 101,514,375.52 | 101,514,375.52 |
| Total payer coverage | 31,097,506.99 | 31,097,506.99 |

All three reconciliation checks passed.

### 9. Key Data Quality Observations

The assessment identified the following areas requiring attention during analysis:

1. Missing clinical reason codes and descriptions.
2. Missing optional patient demographic attributes.
3. Two procedure records with zero elapsed duration.
4. Unusually long encounter durations.
5. Four procedure codes associated with multiple descriptions.
6. Highly skewed procedure costs and total claim costs.
7. Substantial differences in recorded payer coverage across payer categories.
8. Differences between total claim costs and combined encounter and procedure base costs.

These observations will be handled through documented transformations, derived fields, validation checks and appropriate analytical limitations.

### 10. Data Preparation Decisions

The following decisions will guide subsequent preparation:

- Preserve all original raw datasets.
- Do not remove records solely because optional fields contain missing values.
- Retain zero financial values unless a specific analytical rule establishes otherwise.
- Preserve original timestamps and create separate derived duration fields.
- Flag unusual durations rather than deleting or capping them.
- Retain procedure codes and original descriptions for traceability.
- Distinguish NO_INSURANCE from missing or unmatched payer references.
- Validate all joins and aggregations after transformation.
- Exclude patient-identifiable information from public reports, screenshots and GitHub commits.

### 11. Assessment Conclusion

The datasets demonstrate strong structural integrity based on the primary key, foreign key, duplicate and date validation checks completed.

However, several completeness, consistency and distributional issues require consideration during analysis. These include missing optional clinical attributes, unusual encounter durations, inconsistent procedure descriptions and substantial differences in payer coverage patterns.

The datasets are suitable for proceeding to analytical preparation and modelling, provided that the identified limitations are documented and the original source data is preserved.
