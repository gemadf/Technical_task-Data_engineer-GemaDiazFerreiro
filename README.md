# Technical_task-Data_engineer-GemaDiazFerreiro

## 1. Project Overview

This project explores healthcare pricing data published through the UnitedHealthcare (UHC) Transparency in Coverage platform.

The objective is to build a data processing workflow using Python and PySpark, validate the resulting dataset, and perform pricing analysis using PySpark and Snowflake SQL.

The project covers API exploration, file selection, data extraction, transformation, data quality validation, and analytical queries.

## 2. Repository Structure

- `uhc_data_pipeline_and_pricing_analysis.ipynb`: Main notebook containing API exploration, index file selection, data extraction, PySpark transformations, data quality checks, and pricing analysis. The conclusions and key findings from each analysis are documented in Markdown cells throughout the notebook.
- `snowflake_queries.sql`: SQL queries used to inspect and analyse the processed pricing data in Snowflake.
- `df_final.parquet`: Processed dataset exported from PySpark and used to load the data into Snowflake for further analysis.

## 3. Data Source and Scope

The data was obtained from the [UHC Transparency in Coverage platform](https://transparency-in-coverage.uhc.com/), which provides access to index files referencing machine-readable healthcare pricing data.

The workflow retrieved the first 1,000 index files and assessed the size of their associated data files before downloading them. Given the substantial storage and processing requirements, one index file was selected for detailed analysis.

The analysis focuses on a subset of the **In-Network pricing data** associated with the selected index file. The findings are therefore specific to the processed subset and should not be interpreted as representative of the entire UHC dataset.

## 4. Methodology

### 4.1. API Exploration and File Selection

- Retrieved index file metadata from the UHC API.
- Selected index files for inspection.
- Assessed the size of referenced data files before downloading them.
- Selected a subset of files based on their size and feasibility for processing.

### 4.2. Data Extraction and Processing

- Downloaded the selected source files.
- Inspected the JSON structure and schema using PySpark.
- Flattened nested pricing and provider reference information into a tabular dataset.

### 4.3. Data Quality Validation

The processed dataset was checked for:

- Data Quality
- Duplicate Check
- Pricing Data Quality and Overview

### 4.4. Pricing Analysis

PySpark was used to explore pricing distributions across billing codes, including minimum, median, average, and maximum negotiated rates, as well as price variability.

### 4.5. Data Export and Snowflake Analysis

The processed dataset was exported from PySpark to Parquet. The resulting file was manually uploaded to Snowflake using Snowsight and loaded into a table for further analysis.

Additional SQL queries were executed in Snowflake to complement the PySpark analysis.

## 5. Key findings

### Data Quality and Integrity
- **Missing information:** The `severity_of_illness` and `additional_information` columns contain only blank values, while `billing_code_modifier` is populated in only 0.18% of records.
- **Pricing validity:** All 467,861 records have non-null, positive negotiated prices, ranging from $0.14 to $6,615.00.
- **Duplicate detection:** An initial check identified 1,900 duplicate records caused by duplicate entries in the provider reference table. After removing four duplicate provider records, no duplicates remained in the joined dataset.
- **Dataset coverage:** The dataset contains 3,149 distinct billing codes and 4,891 distinct NPIs. All records are classified as `professional`, and approximately 99.998% have a `negotiated` pricing type.

### Pricing Insights
- **Uniform negotiated rates:** Some billing codes have identical prices across the observed records. For example, codes `86769` and `86328` have fixed rates of $42.13 and $45.23, respectively.
- **Price variability across services:** Other billing codes show considerable variation. Code `99204` ranges from $48 to $113, while code `97140` ranges from $13 to $40.
- **Variability in acupuncture pricing:** Code `97813` has negotiated rates ranging from $16.50 to $57, with a median of $40.
- **Differences beyond the median:** For code `97035`, the median price is $10, while the maximum reaches $29.80, illustrating why the full price distribution is useful when analysing negotiated rates.
- **Provider coverage varies by billing code:** The number of distinct NPIs associated with each billing code differs, meaning the volume of available pricing observations varies across services.

Overall, the analysis shows that negotiated pricing patterns differ across billing codes: some services exhibit uniform rates, while others show substantial price variation.

All findings are based on the selected In-Network dataset and should not be interpreted as representative of the entire UHC network.

## 6. Technical Decisions and Limitations

- **File size assessment:** Source file sizes were evaluated before downloading to avoid spending resources on files that could not be processed efficiently.
- **Subset-based processing:** A manageable subset was selected because the full dataset exceeded the available computing resources.
- **Python**: Used to retrieve, process, and analyse data from the UHC API.
- **PySpark:** Used to process nested JSON data and transform it into a tabular structure suitable for analysis.
- **Parquet:** Used as the intermediate export format before loading the processed dataset into Snowflake.
- **Snowflake**: Used to run complementary SQL queries against the processed dataset. The Parquet file was uploaded manually through Snowsight.

The analysis is exploratory and is constrained by the scope of the selected source files and the available computing resources.

## 7. Technologies

- Python
- PySpark
- REST API and JSON
- Parquet
- Snowflake SQL
- Jupyter Notebook / Google Colab

## 8. Reproducibility

The main notebook documents the workflow, processing steps, and analytical decisions.

Reproducing the workflow requires access to the source data and sufficient computing resources. To reproduce the Snowflake analysis, the processed Parquet file must be uploaded to Snowflake, and the corresponding table must be created or loaded before executing the queries in `snowflake_queries.sql`.
