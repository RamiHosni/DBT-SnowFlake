# Sumup Project

## Introduction

In this project, dbt is used with a Snowflake database to explore data
and answer customers' questions.\
As an additional step, the transformed data was pulled from Snowflake
into Power BI to create visual dashboards and KPIs.

## 1. Data Overview

### 1.1 Datasets

There are 3 datasets:

#### Devices

    (id, type, store_id)

#### Transactions

    (id, device_id, product_name, product_sku, category_name, amount,
     status, card_number, cvv, created_at, happened_at)

#### Stores

    (id, name, address, city, country, created_at, typology, customer_id)

Sensitive fields (card_number, cvv) require anonymization. Product data
can be normalized to reduce redundancy.

### 1.2 Data Quality Issues

-   `product_sku` should be unique, but duplicates exist.
-   Some `created_at` values occur before `happened_at`.
-   Files were converted to CSV with UTF‑8 encoding.

## 2. Data Modeling

*(Image placeholders remain because images cannot be extracted from the
PDF automatically.)*

### 2.1 Fact Table --- `Fct_Transactions`

Modifications: - Removed `product_name`, `category_name` - Added
`store_id` from Devices table - Star-schema optimized

PK: `id`\
FK: `product_sku`, `device_id`, `store_id`

### 2.2 Dimension Tables

#### 2.2.1 `Dim_Products`

Normalized product data.\
PK: `product_sku`

#### 2.2.2 `Dim_Devices`

PK: `id`

#### 2.2.3 `Dim_Stores`

PK: `id`

### 2.3 Primary Key Testing

All dbt primary key tests passed.

## 3. Data Aggregation

### 3.1 Top 10 Stores by Transacted Amount

-   Join Stores + Transactions\
-   Filter accepted transactions\
-   Group by store\
-   Sort by total amount

### 3.2 Top 10 Products Sold

-   Count accepted transactions per product_sku\
-   Order by count desc

### 3.3 Average Transacted Amount per Store Typology & Country

-   Join Stores + Transactions\
-   Compute AVG(amount)\
-   Group by typology + country

### 3.4 Percentage of Transactions per Device Type

Formula:

    (count_per_device / total_transactions) * 100

### 3.5 Average Time for a Store to Perform First 5 Transactions

Using two CTEs: - ranked (ROW_NUMBER) - first_five (extract first +
fifth timestamps)

Final output: avg days between first and fifth transaction.

## 4. Data Visualization

Data was loaded into Power BI to produce KPIs and dashboards.

## Credentials (for interviewer)

    URL: https://yn14819.eu-central-1.snowflakecomputing.com/console/login
    User: INTERVIEWER_USER
    Password: Sumup2025@
