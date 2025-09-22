# Sales Data Sample Analysis

This repository contains documentation, cleaning recommendations, analysis outputs, 
and SQL scripts for the provided **sales_data_sample.csv** dataset.

---

##  Dataset Overview
- **Rows:** 2,823
- **Columns:** 25
- **Time range:** 2003–2005
- **Total Sales:** 10,032,628.85
- **Unique Orders:** 307

### Key Columns
- `ORDERNUMBER` – Unique order identifier
- `ORDERDATE` – Date of order (string in original, convert to TIMESTAMP)
- `CUSTOMERNAME`, `CONTACTLASTNAME`, `CONTACTFIRSTNAME`
- `PRODUCTLINE`, `PRODUCTCODE`, `PRODUCTNAME`
- `QUANTITYORDERED`, `PRICEEACH`, `SALES`
- `STATUS` – Order status (Shipped, Cancelled, etc.)
- `YEAR_ID`, `QTR_ID`, `MONTH_ID`
- Location fields: `CITY`, `STATE`, `COUNTRY`, `POSTALCODE`

---

##  Data Quality Findings
1. **SALES mismatch:** ~46% of rows where `SALES != QUANTITYORDERED × PRICEEACH`.
   - Indicates discounts, taxes, or incorrect values.
2. **Dates:** `ORDERDATE` stored as text, must be converted to TIMESTAMP.
3. **Missing values:** Low overall, but present in some columns.
4. **Categorical values:** Some fields need trimming/standardization.

---

##  Recommended Cleaning Steps
- Convert `ORDERDATE` to `TIMESTAMP`.
- Recompute `expected_sales = QUANTITYORDERED * PRICEEACH`.
- Add `SALES_FLAG` column to flag mismatches.
- Standardize categorical fields (`STATUS`, `COUNTRY`, etc.).
- Remove trailing spaces from text columns.

---

##  PostgreSQL Import
1. Use the provided **create_table.sql** to create schema:
   ```sql
   CREATE TABLE sales_data_sample (
       ORDERNUMBER INTEGER,
       QUANTITYORDERED INTEGER,
       PRICEEACH NUMERIC(12,2),
       ORDERLINENUMBER INTEGER,
       SALES NUMERIC(12,2),
       ORDERDATE VARCHAR(255),
       STATUS VARCHAR(255),
       QTR_ID INTEGER,
       MONTH_ID INTEGER,
       YEAR_ID INTEGER,
       PRODUCTLINE VARCHAR(255),
       MSRP INTEGER,
       PRODUCTCODE VARCHAR(255),
       CUSTOMERNAME VARCHAR(255),
       PHONE VARCHAR(255),
       ADDRESSLINE1 VARCHAR(255),
       ADDRESSLINE2 VARCHAR(255),
       CITY VARCHAR(255),
       STATE VARCHAR(255),
       POSTALCODE VARCHAR(255),
       COUNTRY VARCHAR(255),
       TERRITORY VARCHAR(255),
       CONTACTLASTNAME VARCHAR(255),
       CONTACTFIRSTNAME VARCHAR(255),
       DEALSIZE VARCHAR(255)
   );
   ```

2. Load data with COPY:
   ```sql
   COPY sales_data_sample
   FROM '/path/to/sales_data_sample.csv'
   CSV HEADER DELIMITER ',';
   ```

3. Suggested indexes:
   ```sql
   CREATE INDEX idx_ordernumber ON sales_data_sample (ORDERNUMBER);
   CREATE INDEX idx_orderdate ON sales_data_sample (ORDERDATE);
   CREATE INDEX idx_customername ON sales_data_sample (CUSTOMERNAME);
   ```

---

##  Deliverables
- **sales_analysis_report.xlsx** → Full analysis workbook
- **create_table.sql** → SQL schema for PostgreSQL
- **recommendations.txt** → Cleaning & analysis recommendations
- **plots/** → Charts (histogram, boxplot, monthly sales, correlation)

---

##  Next Steps / Advanced Analysis
- Perform **RFM segmentation** on customers
- Forecast monthly sales (ARIMA/Prophet)
- Study impact of discounts by reconciling `SALES` mismatch rows

---


