# Olist Brazilian E-Commerce Dataset

This project uses the official **Olist Brazilian E-Commerce Public Dataset**.

## Overview

The Olist dataset is a real-world Brazilian e-commerce dataset containing transactional data collected from an online marketplace.

It is a relational dataset consisting of multiple interconnected tables, including customer information, orders, products, sellers, payments, reviews, and geolocation data.

The dataset is widely used for machine learning, data mining, and data analysis research involving structured tabular data.

## Dataset Contents

The dataset contains the following CSV files:

```text
raw/
├── olist_customers_dataset.csv
├── olist_geolocation_dataset.csv
├── olist_order_items_dataset.csv
├── olist_order_payments_dataset.csv
├── olist_order_reviews_dataset.csv
├── olist_orders_dataset.csv
├── olist_products_dataset.csv
├── olist_sellers_dataset.csv
└── product_category_name_translation.csv
```

## Why This Dataset?

The Olist dataset represents a real-world tabular dataset containing:

- Missing values
- Categorical features
- Numerical features
- Relational data
- Transaction records
- Customer behavior information

Its diverse data structure makes it suitable for evaluating the proposed **Data Reliability Score (DRS)** under different tabular data quality conditions.

## Download

This dataset is **not included** in this repository because it is publicly available and consists of large static files.

Download the official dataset and extract all CSV files into the `raw/` directory.

## Notes

The Olist dataset is a **static public dataset** whose contents do not change over time.

To ensure reproducibility, the original files should remain unchanged before preprocessing.
