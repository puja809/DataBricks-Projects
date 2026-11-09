# E-commerce Medallion Data Platform (Databricks)

End-to-end e-commerce analytics project built on Databricks with a medallion architecture.

## Architecture

Raw CSV data lands in a Unity Catalog volume (`ecommerce.source_data.raw_data`), and is processed
layer by layer into the `ecommerce` catalog:

```
Volume (raw CSVs)
  -> ecommerce.bronze.*   (raw ingestion, + source_file / ingested_at metadata)
  -> ecommerce.silver.*   (cleaning, dedup, type casting, standardization)
  -> ecommerce.gold.*     (conformed dimensions + fact table + fact_view_main)
  -> AI/BI dashboard      (sales analytics over the gold layer)
```

## Contents

* `project_ecommerce/projet_setup/project_ecommerce_setup.ipynb` - creates the catalog and schemas.
* `project_ecommerce/Medallion_processing_dim/` - bronze/silver/gold transformation notebooks (16 notebooks).
* `project_ecommerce/projet_setup/dim_gold_4.ipynb`, `dim_gold_5.ipynb` - gold fact tables + fact view.
* `Ecommerce.lvdash.json` - AI/BI dashboard definition (sales trends by category/month).
* `job_definitions/` - exported Lakeflow Job definitions:
  * `notebook_orchestration.json` - runs all 18 notebooks in dependency order (bronze -> silver -> gold).
  * `table_orchestration_dashboard_refresh.json` - refreshes the published dashboard after gold updates.
  * `parent_orchestration.json` - runs both jobs in sequence (single entry point).

## Usage

1. Run `project_ecommerce_setup.ipynb` once to create the catalog and schemas.
2. Drop raw files into `/Volumes/ecommerce/source_data/raw_data/ecomm-raw-data/<entity>/`.
3. Run the parent orchestration job (see `job_definitions/parent_orchestration.json`) - it rebuilds
   all bronze/silver/gold tables and refreshes the dashboard.
