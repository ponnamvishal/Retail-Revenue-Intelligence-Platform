# Retail Revenue Intelligence Platform

An end-to-end retail analytics warehouse built using Python, SQL, dbt, DuckDB, and Power BI.

The project transforms raw e-commerce transaction data into tested analytical models and business-facing dashboards for customer, sales, product, and revenue analysis.

> **Project distinction:** This warehouse focuses on transactional sales and revenue reporting using the Brazilian Olist dataset. It is separate from **Digital Commerce Behavior & Conversion Intelligence**, which analyzes October 2019 clickstream events, sessions, and sequential conversion funnels using PostgreSQL.

## Project Overview

This project builds a layered analytics warehouse from raw e-commerce data.

The workflow is:

Raw CSV Data → Python Data Loading → dbt Staging → Intermediate Transformation → Analytics Marts → Data Quality Audits → Power BI

The warehouse separates raw data, transformations, business logic, validation, and reporting so that analytical results can be traced through the pipeline.

## Tech Stack

- Python
- SQL
- DuckDB
- dbt
- Power BI
- Git / GitHub

## Dataset

The project uses the Brazilian E-Commerce Public Dataset by Olist.

The raw dataset contains information covering:

- Customers
- Orders
- Order items
- Payments
- Reviews
- Products
- Sellers
- Geolocation
- Product category translations

Raw CSV files are intentionally excluded from Git because the complete source dataset is approximately 120 MB.

The pipeline expects the source files under `data/raw/`.

## Warehouse Architecture

### 1. Raw Layer

The raw Olist CSV files are loaded into DuckDB without applying business transformations.

### 2. Staging Layer

The staging models standardize and prepare the raw source tables for downstream transformations.

Source tables include customers, geolocation, orders, order items, order payments, reviews, products, sellers, and category translations.

### 3. Intermediate Layer

The intermediate model enriches order-item records by combining orders, customers, products, sellers, and category translations. This creates an order-item-level analytical dataset.

### 4. Marts Layer

The warehouse contains three main analytical marts.

#### Customer Summary

Provides total orders, delivered orders, total items, product revenue, freight, total revenue, average order value, first and last order dates, and customer lifetime days.

#### Daily Sales

Provides total orders, total items, product revenue, freight, total revenue, and average order value.

#### Product Performance

Provides total orders, items sold, product revenue, freight, total revenue, average item price, average freight value, and product/category attributes.

## Revenue Definitions

- **Product revenue** = item price only
- **Freight revenue** = freight value
- **Total revenue** = item price + freight value
- **Payment value** = recorded customer payment amount

Payment value is kept as a separate financial measure rather than being treated as a direct reconciliation target for product revenue.

## Data Quality & Validation

The project includes dbt tests and custom validation queries covering model integrity and warehouse consistency.

The recorded final dbt test run passed:

- 59 data tests
- 59 passed
- 0 warnings
- 0 errors

Additional reconciliation checks validate raw-to-staging order counts, staging-to-mart product revenue, staging-to-mart total revenue, orders without item records, payment values, and data-quality exceptions.

### Order Reconciliation

Verified warehouse figures include:

- Raw orders: 99,441
- Orders represented in order items: 98,666
- Orders without items: 775
- Product revenue: BRL 13,591,643.70
- Freight: BRL 2,251,909.54
- Payment value: BRL 16,008,872.12

The staging-to-mart product revenue reconciliation has a difference of BRL 0.00. Orders without item records are separately audited rather than silently removed.

## Data Quality Audit

The audit model identifies orders without associated order-item records when their status is not expected to lack item records.

The current audit identifies three exceptions: two invoiced orders and one shipped order. These records are retained and surfaced as data-quality exceptions rather than deleted.

## Power BI

The Power BI dashboard provides business-facing analysis based on the warehouse marts, focusing on sales performance, customer analysis, product performance, revenue trends, and KPI monitoring.

The Power BI file is available under:

`power bi/Ecommerce_Customer_Revenue_Analytics_Final.pbix`

## Project Structure

```text
ecommerce-analytics-warehouse/
├── ecommerce_analytics/
│   ├── models/
│   │   ├── staging/
│   │   ├── intermediate/
│   │   ├── marts/
│   │   └── audits/
│   ├── macros/
│   ├── tests/
│   ├── analyses/
│   ├── seeds/
│   ├── snapshots/
│   └── dbt_project.yml
├── scripts/
│   ├── load_raw.py
│   └── reconcile_orders.py
├── docs/
│   └── architecture.md
├── power bi/
│   └── Ecommerce_Customer_Revenue_Analytics_Final.pbix
├── requirements.txt
├── .gitignore
└── README.md
```
