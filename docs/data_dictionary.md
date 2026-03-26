# Data Dictionary — Online Retail II
## Source
- Dataset: Online Retail II
- Provider: UCI Machine Learning Repository
- URL: https://archive.ics.uci.edu/dataset/502/online+retail+ii

## Table: online_retail_raw

| Column | Data Type | Description | Example | Notes |
|--------|-----------|-------------|---------|-------|
| InvoiceNo | VARCHAR | Unique invoice number per transaction | 489434 | Starts with 'C' = cancelled order |
| StockCode | VARCHAR | Unique product code | 85048 | Some codes are non-product (POST, DOT, M) |
| Description | VARCHAR | Product name | WHITE HANGING HEART T-LIGHT HOLDER | Can be NULL, inconsistent casing |
| Quantity | INTEGER | Number of units in the transaction | 6 | Negative = return/refund |
| InvoiceDate | DATETIME | Date and time of transaction | 2009-12-01 07:45:00 | Used for time analysis |
| UnitPrice | FLOAT | Price per unit in GBP (£) | 2.55 | Zero or negative = test/adjustment |
| CustomerID | FLOAT | Unique customer identifier | 13085 | ~25% NULL (guest checkouts) |
| Country | VARCHAR | Country where customer resides | United Kingdom | ~90% UK |

## Key Business Rules
1. One InvoiceNo can have multiple rows (multiple products per order)
2. InvoiceNo starting with 'C' must be excluded from analysis
3. CustomerID is required for basket analysis
4. Quantity × UnitPrice = Line Revenue
```