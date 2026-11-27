# Hospital Operations, Cost & Patient Flow Intelligence

## Project Overview

This project analyses hospital operations, encounter patterns, healthcare costs, payer coverage and procedure utilisation using historical healthcare datasets provided through the AnalystLab Africa Experience Lab Internship Programme (Week 3 – Data Analytics Track).

The project develops an interactive Healthcare Operations, Cost & Patient Flow Intelligence Dashboard using Python, dimensional data modelling, DAX and Microsoft Power BI.

The objective is to transform raw healthcare data into meaningful operational and financial insights that support evidence-based decision-making.

## Business Objectives

- Analyse hospital encounter volumes and trends over time.
- Examine encounter classifications and duration patterns.
- Identify encounters exceeding 24 hours.
- Investigate healthcare claim costs and payer coverage.
- Analyse procedure frequency, utilisation and base costs.
- Identify operational and financial patterns requiring further investigation.
- Develop evidence-based recommendations supported by analytical findings.

## Tools & Technologies

| Area | Tools |
| --- | --- |
| Programming and data analysis | Python, pandas, NumPy |
| Data preparation | Python, pandas, Power Query |
| Database and querying | PostgreSQL, SQL |
| Business intelligence | Microsoft Power BI |
| Data modelling and calculations | Dimensional Modelling, DAX |
| Development environment | Visual Studio Code, Jupyter Notebook |
| Version control | Git and GitHub |

## Dataset Overview

The project uses five primary healthcare datasets and a supporting data dictionary.

| Dataset | Records |
| --- | ---: |  
| Encounters | 27,891 |
| Patients | 974 |
| Procedures | 47,701 |
| Payers | 10 |
| Organisations | 1 |

**Reporting period:** 2 January 2011 – 5 February 2022.

The datasets were assessed for completeness, duplicate records, key integrity, date validity, financial consistency and referential integrity.

Raw and processed datasets are excluded from this repository to protect data integrity and avoid exposing patient-related information.

## Analytical Approach

### 1. Data Inspection and Quality Assessment

- Examined dataset dimensions, data types and missing values.
- Checked exact duplicate records and primary key uniqueness.
- Validated foreign key relationships.
- Investigated date fields, encounter durations and financial values.
- Identified unusual values and potential source-data limitations.
- Documented data preparation decisions.

### 2. Data Preparation and Modelling

Prepared two fact tables and five dimension tables.

**Fact tables**  

- `fact_encounters` – One row per encounter.
- `fact_procedures` – One row per procedure.

**Dimension tables**  

- `dim_patients`
- `dim_payers`
- `dim_organizations`
- `dim_encounter_class`
- `dim_date`

The dimensional model uses one-to-many relationships from dimensions to fact tables. The two fact tables remain separate to preserve their different levels of detail and prevent duplication of encounter-level financial measures.

### 3. Power BI Dashboard Development

The interactive dashboard contains five analytical pages:

| Page | Focus |
| --- | --- |
| Executive Overview | Overall encounter, patient, financial and procedure KPIs |
| Encounter Analysis | Encounter volumes, class distribution and duration |
| Financial & Payer Analysis | Claim costs, payer coverage and zero-coverage encounters |
| Procedure Utilisation | Procedure frequency, utilisation and cost patterns |
| Data & Methodology | Data sources, model design, KPI definitions, validation and limitations |

Interactive filters support exploration by year, encounter class and payer.

### 4. Validation

Key Power BI measures were independently compared with Python calculations.

Validation covered:

- Encounter and patient counts.
- Encounter duration statistics.
- Encounters exceeding 24 hours.
- Claim costs and payer coverage.
- Procedure counts and average procedure base costs.
- Encounters with procedures and procedure utilisation.

## Selected Analytical Findings

| Indicator | Finding |
| --- | ---: |
| Total encounters | 27,891 |
| Distinct patients | 974 |
| Total procedures | 47,701 |
| Average encounter duration | 7.27 hours |
| Encounters exceeding 24 hours | 75 |
| Total claim cost | 101,514,375.52 |
| Total payer coverage | 31,097,506.99 |
| Encounters with zero payer coverage | 13,586 |
| Encounters with procedures | 14,670 |
| Procedure utilisation | 52.60% |
| Average procedure base cost | 2,212.06 |

These findings describe the supplied historical dataset and should not be interpreted as current hospital performance.

## Key Recommendations

The analysis identifies several areas for further investigation:

1. **Prolonged encounters:** Review encounters exceeding 24 hours, particularly inpatient records, and validate the underlying timestamps against actual admission and discharge information.
2. **Payer coverage:** Investigate zero-coverage records by payer category and distinguish the `NO_INSURANCE` category from other payer records.
3. **High-cost procedures:** Review high-cost procedures, their costing assumptions and resource requirements.
4. **High-value claims:** Examine unusually high claim records alongside associated procedures and supporting cost information.

These recommendations identify opportunities for investigation rather than confirmed operational or financial failures.

## Project Structure

```text
Hospital_Operations_Cost_Patient_Flow/
│
├── data/
│   ├── raw/
│   └── processed/
│
├── notebooks/
│   ├── 01_data_inspection.ipynb
│   └── 02_data_preparation_and_modelling.ipynb
│
├── scripts/
│
├── sql/
│   └── reference/
│
├── documentation/
│   ├── data_quality_assessment.md
│   ├── data_model_design.md
│   ├── dashboard_validation.md
│   └── kpi_definitions.md
│
├── reports/
│   └── executive_summary.md
│
├── powerbi/
│   ├── dashboard/
│   │   └── Hospital_Operations_Cost_Patient_Flow.pbix
│   └── screenshots/
│
├── README.md
├── requirements.txt
└── .gitignore
```

## Deliverables

- Interactive Power BI dashboard (`.pbix`).
- Five dashboard screenshots.
- Data quality assessment.
- Dimensional data model documentation.
- KPI definitions and calculation logic.
- Dashboard validation documentation.
- Executive summary with findings and recommendations.
- Python notebooks documenting data inspection and preparation.

## Project Status

**Completed – AnalystLab Africa Experience Lab Internship Programme, Week 3: Data Analytics Track.**

The project demonstrates an end-to-end analytical workflow covering data inspection, data quality assessment, preparation, dimensional modelling, DAX calculations, dashboard development, validation and business recommendations.

## Important Notes

- The dataset represents historical records ending in February 2022.
- The source currency was not specified, so financial values are presented without a currency symbol.
- Zero payer coverage does not necessarily mean a patient was uninsured.
- Encounter duration is calculated from elapsed timestamps and is not automatically equivalent to inpatient length of stay.
- The available data does not establish a reliable patient readmission or return-visit definition.
- Patient-identifiable demographic and contact details are excluded from the analytical model.
  