# E-Commerce Data Platform Modernization: Migrating from AWS Legacy Stack to Databricks Lakehouse

## Project Overview
This project demonstrates the end-to-end modernization of a data platform for an on-premises e-commerce store. The goal was to solve the operational complexities, high maintenance overhead, and data silos of a legacy AWS infrastructure by migrating to a unified **Databricks Lakehouse architecture**. 

By consolidating fragmented compute services (Lambda, Glue, EC2) and a separate data warehouse (Redshift) into Databricks, the platform now handles data ingestion, transformation, dimensional modeling, and BI serving through a single, scalable pane of glass.

---

## Architectural Evolution

### 1. The Legacy Architecture (Before)
In the initial phase, data was extracted from the on-prem transactional Relational DB and staged in Amazon S3. From there, processing became highly fragmented:
* **ETL & Compute Silos:** Data processing was split across AWS Lambda (for real-time/lightweight tasks), AWS Glue (for managed spark ETLs), and Amazon EC2 (for custom cron-scheduled heavy transformations).
* **Data Redundancy:** Transformed data had to be explicitly loaded into Amazon Redshift for data warehousing.
* **Downstream Bottleneck:** BI tools and business teams queried Redshift, creating a hard separation between data processing and data serving layers.

View the legacy setup design here: [![Legacy Architecture](legacy_architecture.jpg)](legacy_architecture.png)`

### 2. The Modernized Databricks Architecture (After)
To eliminate tool sprawl and simplify management, the architecture was streamlined:
* **Simplified Ingestion:** Data securely lands from the source into an Amazon S3 landing zone.
* **Unified Compute & Storage Layer:** AWS Lambda, Glue, EC2, and Amazon Redshift were entirely replaced by **Databricks** (acting as both the ETL Compute engine and the Data Warehouse via Delta Lake).
* **Direct BI Serving:** The BI/Reporting layer now queries Databricks directly, drastically reducing data latency and infrastructure maintenance.

View the updated setup design here: [![Databricks Architecture](databricks_architecture.jpg)](databricks_architecture.png)

---

## Technical Stack & Tools
* **Source System:** On-Premises E-commerce Relational Database
* **Cloud Storage & Data Lake:** Amazon S3 (Staging and Landing zones)
* **Unified Data Platform:** Databricks (PySpark & Spark SQL for ETL compute, Delta Lake for storage)
* **Data Modeling:** Star Schema (Dimension and Fact tables managed via Medallion Architecture)
* **Visualization:** BI/Reporting Tools (e.g., Power BI / Tableau / Metabase)

---

## Repository Structure & Pipeline Flow

The platform's processing engine is written using native Spark notebooks inside Databricks, structured into a clean execution sequence:

```text
├── README.md
├── legacy_architecture.jpg
├── databricks_architecture.jpg
├── 1_setup/
│   └── platform_configuration.ipynb      # Cluster parameters, mount points, and environment setup
├── 2_medallion_processing_dim/
│   └── dim_tables_pipeline.ipynb         # PySpark/SQL processing for cleaned Dimension tables (Bronze -> Silver -> Gold)
└── 3_medallion_processing_fact/
    └── fact_tables_pipeline.ipynb        # PySpark/SQL processing for high-volume Fact tables (Bronze -> Silver -> Gold)
