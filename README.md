# Analytics Data Platform: Sydney Airbnb & Census ELT

A production-ready ELT data pipeline utilizing **Apache Airflow**, **dbt Cloud**, and **Postgres** to analyze Airbnb performance and Census demographics in Sydney. This project implements a full Medallion Architecture to transform raw data into analytical insights.

## 🏗 Architecture Overview

The project follows a **Medallion Architecture**:

* **Bronze (Raw):** Ingestion of raw Airbnb CSVs (12 months), Census G01/G02 tables, and LGA mappings.
* **Silver (Cleansed):** Data cleaning, standardised naming, and **SCD Type 2 Snapshots** for all dimension entities to track changes over time.
* **Gold (Curated):** A Star Schema consisting of optimized Fact and Dimension tables.
* **Data Marts:** Final analytical views (SCD2 logic applied) for business intelligence.

## 🚀 Repository Structure

| File / Folder | Description |
| :--- | :--- |
| `ELT_Pipeline/bronze_schema_creation.py` | Airflow DAG script for orchestrating data loading into Postgres. |
| `ELT_Pipeline/part_1.sql` | SQL script for Bronze schema setup and raw table DDLs. |
| `ELT_Pipeline/part_4.sql` | Ad-hoc analysis queries answering key business and demographic questions. |
| `ELT_Pipeline/dbt/models/` | dbt transformation logic for Silver, Gold, and Data Mart layers. |
| `ELT_Pipeline/dbt/snapshots/` | dbt snapshot files for implementing SCD Type 2 on dimensions. |
| `ELT_Pipeline/dbt_project.yml` | Configuration file for the dbt project. |

## 🛠 Key Components

### Data Orchestration (Airflow)
The DAG in `bronze_schema_creation.py` automates the ingestion of Airbnb and Census data from cloud storage into the Postgres Bronze layer. It is designed to process 12 months of data sequentially to ensure data integrity.

### Data Modeling (dbt)
* **Snapshots:** Created for dimensions (Host, Suburb, LGA, etc.) using a `timestamp` strategy to track historical changes.
* **Star Schema:** The Gold layer separates metrics (Price, Availability) into Fact tables and descriptive attributes into Dimension tables.
* **SCD Type 2 Join:** Data marts utilize dbt snapshot logic to ensure fact records are joined with the dimension version that was valid at that specific point in time.

### Analytical Data Marts (`part_4.sql`)
The platform exposes three primary views for analysis:
* **`dm_listing_neighbourhood`**: Metrics by neighbourhood and month.
* **`dm_property_type`**: Performance by property and room types.
* **`dm_host_neighbourhood`**: Revenue insights at the LGA level.

## 📈 Key Metrics Defined
* **Active Listing Rate:** Percentage of listings with `has_availability = 't'`.
* **Estimated Revenue:** Calculated as `(30 - availability_30) * price`.
* **Superhost Rate:** Ratio of distinct Superhosts to total distinct hosts.
