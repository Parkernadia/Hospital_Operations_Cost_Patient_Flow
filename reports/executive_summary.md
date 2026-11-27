# Executive Summary
## Hospital Operations, Cost & Patient Flow Intelligence

### 1. Project Overview

This project analyses healthcare encounter activity, patient volumes, encounter duration, claim costs, payer coverage and procedure utilisation.

The objective is to provide a consolidated view of operational and financial patterns through a dimensional data model and an interactive Power BI dashboard.

The analysis uses five primary datasets covering:

| Dataset | Records |
|---|---:|
| Encounters | 27,891 |
| Patients | 974 |
| Procedures | 47,701 |
| Payers | 10 |
| Organisations | 1 |

The reporting period runs from 2 January 2011 to 5 February 2022.

### 2. Key Findings

#### Encounter activity and duration

The dataset contains 27,891 encounters involving 974 distinct patients. Average elapsed encounter duration was 7.27 hours, while the median was 0.25 hours.

Seventy-five encounters exceeded 24 hours. Inpatient encounters accounted for 58 of these records.

The difference between the mean and median, together with a maximum recorded duration of 44,930 hours, indicates that extreme values influence the average.

#### Claim costs and payer coverage

Total recorded claim cost was 101,514,375.52, while total recorded payer coverage was 31,097,506.99.

A total of 13,586 encounter records had zero recorded payer coverage.

This finding warrants further investigation, but zero coverage should not be interpreted as proof that the associated patients were uninsured. The explicit `NO_INSURANCE` payer category must be distinguished from other payer records.

#### Procedure utilisation

The dataset contains 47,701 procedure records across 14,670 distinct encounters.

Procedure utilisation was 52.60% of all encounters.

Average procedure base cost was 2,212.06 per procedure record. ICU admission had the highest identified average procedure base cost at 206,260.40 across five records.

High average cost does not necessarily indicate high total expenditure. Procedure frequency and total base cost should be considered alongside average cost.

#### High-value claims

The highest recorded encounter claim cost was 641,882.70. The associated encounter included ten procedures, with recorded procedure base costs totalling 641,458.

This is a high-value record suitable for review. It is not, by itself, evidence of incorrect billing or financial loss.

### 3. Recommendations

#### Recommendation 1: Review encounters exceeding 24 hours

**Finding:** Seventy-five encounters exceeded 24 hours, including 58 inpatient encounters.

**Action:** Review the underlying encounter records and validate start and stop timestamps against actual admission, discharge and bed-management records. Investigate the extreme duration values.

**Expected benefit:** Better understanding of prolonged encounters and potential operational bottlenecks.

**Limitation:** The available timestamps measure elapsed time and may not represent verified inpatient length of stay.

#### Recommendation 2: Investigate zero payer coverage

**Finding:** 13,586 encounter records had zero recorded payer coverage.

**Action:** Segment these encounters by payer category, distinguish `NO_INSURANCE` from other payer records and investigate eligibility, claim adjudication and coverage recording processes.

**Expected benefit:** Improved understanding of payer coverage patterns and potential opportunities to strengthen billing and coverage processes.

**Limitation:** Zero recorded coverage does not establish that a patient was uninsured or that a claim was unpaid.

#### Recommendation 3: Review high-cost procedures

**Finding:** ICU admission had an average procedure base cost of 206,260.40 across five records.

**Action:** Review the costing methodology, resource requirements and supporting records for high-cost procedures. Compare average cost, procedure frequency and total procedure base cost.

**Expected benefit:** Better visibility into high-cost services and more informed resource and costing reviews.

**Limitation:** High cost alone does not demonstrate inefficiency or inappropriate clinical practice. Clinical context and resource-use data are required.

#### Recommendation 4: Investigate high-value claims

**Finding:** The highest recorded claim cost was 641,882.70 and was associated with ten procedures.

**Action:** Review the claim and associated procedure records, including coding, cost allocation and payer processing information.

**Expected benefit:** Improved understanding of unusually high claim values and potential data or processing issues.

**Limitation:** The source data does not establish a formal equation between claim cost and the sum of encounter and procedure base costs. A difference should not automatically be treated as an error.

### 4. Limitations

The analysis has several limitations:

- Data ends in February 2022 and does not represent current performance.
- Encounter duration is based on elapsed timestamps and is not necessarily inpatient length of stay.
- Extreme duration values affect averages.
- The source currency was not specified.
- Zero payer coverage does not necessarily indicate uninsured patients.
- Only one organisation is represented.
- The available fields do not establish a reliable readmission or return-visit definition.
- Staffing, bed capacity, clinical outcomes and payer adjudication data were not supplied.

### 5. Conclusion

The analysis provides a structured view of encounter activity, duration, financial patterns, payer coverage and procedure utilisation.

The dimensional model and independently validated measures provide a foundation for exploring the supplied data. The dashboard highlights areas that warrant further operational and financial investigation while distinguishing observed patterns from conclusions that require additional evidence.

Further analysis using current operational records, verified length-of-stay data, staffing information, clinical context and payer adjudication records would strengthen decision-making.