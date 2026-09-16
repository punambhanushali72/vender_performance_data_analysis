# Vendor Performance Analysis

Analysis of vendor purchasing, sales, and profitability data to identify underperforming vendors, pricing inefficiencies, and inventory risk — using a SQL-driven ETL pipeline and Python for exploratory and statistical analysis.

## Overview

Retail/inventory businesses often hold purchase, sales, and pricing data in separate systems, making it hard to see vendor performance at a glance. This project builds a single source of truth — a vendor sales summary table — and uses it to answer real business questions: which vendors drive the most procurement spend, which brands are priced or promoted poorly, and whether performance differences between vendors are statistically meaningful or just noise.

## Pipeline

```
Raw CSVs  →  SQLite ingestion  →  SQL summary table  →  Data cleaning  →  EDA  →  Vendor performance analysis
```

1. **Ingestion** (`ingestion_db.py`) — Loads raw CSV files from `data/` into a SQLite database (`inventory.db`) using SQLAlchemy, with logging for traceability.
2. **Vendor summary** (`get_vendor_summary.py`) — Joins purchases, sales, freight, and pricing tables with a multi-CTE SQL query into one vendor-and-brand-level summary table, then cleans it and engineers key metrics: `GrossProfit`, `ProfitMargin`, `StockTurnover`, `SalesToPurchaseRatio`.
3. **Exploratory Data Analysis** (`Exploratory_data_analysis.ipynb`) — Inspects the raw tables, validates relationships between them, builds and sanity-checks the summary query, and loads the cleaned result back into the database.
4. **Vendor Performance Analysis** (`Vendor_Performance_Analysis.ipynb`) — Deeper analysis on the summary table: distribution and outlier checks, correlation analysis, vendor/brand segmentation, and hypothesis testing.

## Key Findings

- **Vendor concentration:** The top 10 vendors account for **65.69%** of total procurement spend, indicating significant supplier dependency.
- **Bulk purchasing impact:** Unit purchase price drops from **$39.06** (small orders) to **$10.78** (large orders) — a ~72% reduction — showing clear cost savings from bulk buying.
- **Pricing/promotion targets:** A subset of brands combine low sales volume with high profit margins, flagging them as candidates for promotional or pricing adjustments.
- **Inventory risk:** Several vendors show stock turnover below 1, and a meaningful amount of capital is tied up in unsold inventory, concentrated among a small set of vendors.
- **Statistical validation:** A two-sample t-test (95% CI) confirms that low-sales vendors sustain significantly higher profit margins than top-sales vendors (p < 0.05), suggesting premium pricing or lower operating costs rather than random variation.

## Tech Stack

- **Python:** Pandas, NumPy
- **Database:** SQLite, SQLAlchemy
- **Visualization:** Matplotlib, Seaborn
- **Statistics:** SciPy (t-test, confidence intervals)

## Repository Structure

```
├── ingestion_db.py                     # CSV → SQLite ingestion pipeline
├── get_vendor_summary.py               # SQL join + cleaning + feature engineering
├── Exploratory_data_analysis.ipynb     # Table inspection, summary query build, validation
├── Vendor_Performance_Analysis.ipynb   # EDA, segmentation, hypothesis testing
├── logs/                               # Ingestion & processing logs (generated)
└── README.md
```

> Raw data (`data/`) and the generated database (`inventory.db`) are not tracked in this repository — see `.gitignore`.

## How to Run

1. Place source CSV files in a `data/` folder in the project root.
2. Run the ingestion script to build the database:
   ```
   python ingestion_db.py
   ```
3. Generate the vendor sales summary table:
   ```
   python get_vendor_summary.py
   ```
4. Open `Exploratory_data_analysis.ipynb` and `Vendor_Performance_Analysis.ipynb` to reproduce the analysis and visualizations.

## Next Steps

- Automate the pipeline as a scheduled job for recurring vendor reporting.
- Build a dashboard (Power BI/Tableau) on top of `vendor_sales_summary` for non-technical stakeholders.
- Extend the analysis to forecast vendor performance trends over time.
