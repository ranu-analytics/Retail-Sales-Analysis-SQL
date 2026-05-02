#  Retail Sales Analysis — SQL Project

![SQL](https://img.shields.io/badge/Tool-SQL-blue) ![PostgreSQL](https://img.shields.io/badge/Database-PostgreSQL-336791) ![Status](https://img.shields.io/badge/Status-Completed-brightgreen) ![Records](https://img.shields.io/badge/Records-2000-orange)

##  Project Overview

This project performs end-to-end retail sales analysis using **SQL (PostgreSQL)** on a dataset of **2000 transactions** spanning **2022–2023**. The analysis covers data cleaning, exploration, and answering key business questions related to sales performance, customer behavior, and product categories.

---

##  Objective

To analyze retail sales data and extract meaningful business insights such as:
- Which product categories generate the most revenue?
- Who are the top customers?
- What are the busiest sales shifts during the day?
- How do sales vary across months and years?

---

##  Database Schema

### Table: `retailsales`

| Column | Data Type | Description |
|---|---|---|
| `transactions_id` | INT (PK) | Unique transaction identifier |
| `sale_date` | DATE | Date of the sale |
| `sale_time` | TIME | Time of the sale |
| `customer_id` | INT | Unique customer identifier |
| `gender` | VARCHAR(15) | Customer gender |
| `age` | INT | Customer age |
| `category` | VARCHAR(15) | Product category (Clothing, Beauty, Electronics) |
| `quantiy` | INT | Quantity sold |
| `price_per_unit` | FLOAT | Price per unit |
| `cogs` | FLOAT | Cost of goods sold |
| `total_sale` | FLOAT | Total sale amount |

### Schema Setup

```sql
CREATE TABLE retailsales
(
    transactions_id  INT PRIMARY KEY,
    sale_date        DATE,
    sale_time        TIME,
    customer_id      INT,
    gender           VARCHAR(15),
    age              INT,
    category         VARCHAR(15),
    quantiy          INT,
    price_per_unit   FLOAT,
    cogs             FLOAT,
    total_sale       FLOAT
);
```

---

##  Data Cleaning

Before analysis, NULL values were identified and removed to ensure data quality.

```sql
-- Check for NULL values across all key columns
SELECT * FROM retail_sales
WHERE 
    transaction_id IS NULL
    OR sale_date IS NULL
    OR sale_time IS NULL
    OR gender IS NULL
    OR category IS NULL
    OR quantity IS NULL
    OR cogs IS NULL
    OR total_sale IS NULL;

-- Delete records with NULL values
DELETE FROM retail_sales
WHERE 
    transaction_id IS NULL
    OR sale_date IS NULL
    OR sale_time IS NULL
    OR gender IS NULL
    OR category IS NULL
    OR quantity IS NULL
    OR cogs IS NULL
    OR total_sale IS NULL;
```

---

##  Data Exploration

```sql
-- Total number of sales
SELECT COUNT(*) AS total_sale FROM retail_sales;

-- Total unique customers
SELECT COUNT(DISTINCT customer_id) AS total_customers FROM retail_sales;

-- Available product categories
SELECT DISTINCT category FROM retail_sales;
```

---

## 📊 Business Questions & SQL Queries

---

### Q1. Sales on a Specific Date
**Retrieve all columns for sales made on '2022-11-05'.**

```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
```

---

### Q2. Clothing Sales Filter
**Retrieve all transactions where the category is 'Clothing' and quantity sold is 4 or more in November 2022.**

```sql
SELECT *
FROM retail_sales
WHERE 
    category = 'Clothing'
    AND TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'
    AND quantity >= 4;
```

---

### Q3. Total Sales by Category
**Calculate the total sales and total orders for each category.**

```sql
SELECT 
    category,
    SUM(total_sale) AS net_sale,
    COUNT(*)        AS total_orders
FROM retail_sales
GROUP BY 1;
```

---

### Q4. Average Age of Beauty Customers
**Find the average age of customers who purchased items from the 'Beauty' category.**

```sql
SELECT
    ROUND(AVG(age), 2) AS avg_age
FROM retail_sales
WHERE category = 'Beauty';
```

---

### Q5. High Value Transactions
**Find all transactions where the total sale is greater than 1000.**

```sql
SELECT *
FROM retail_sales
WHERE total_sale > 1000;
```

---

### Q6. Transactions by Gender and Category
**Find the total number of transactions made by each gender in each category.**

```sql
SELECT 
    category,
    gender,
    COUNT(*) AS total_trans
FROM retail_sales
GROUP BY category, gender
ORDER BY 1;
```

---

### Q7. Best Selling Month per Year
**Calculate the average sale for each month and find the best selling month in each year.**

```sql
SELECT 
    year,
    month,
    avg_sale
FROM (
    SELECT 
        EXTRACT(YEAR FROM sale_date)  AS year,
        EXTRACT(MONTH FROM sale_date) AS month,
        AVG(total_sale)               AS avg_sale,
        RANK() OVER(
            PARTITION BY EXTRACT(YEAR FROM sale_date) 
            ORDER BY AVG(total_sale) DESC
        )                             AS rank
    FROM retail_sales
    GROUP BY 1, 2
) AS t1
WHERE rank = 1;
```

---

### Q8. Top 5 Customers
**Find the top 5 customers based on highest total sales.**

```sql
SELECT 
    customer_id,
    SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5;
```

---

### Q9. Unique Customers per Category
**Find the number of unique customers who purchased items from each category.**

```sql
SELECT 
    category,
    COUNT(DISTINCT customer_id) AS cnt_unique_cs
FROM retail_sales
GROUP BY category;
```

---

### Q10. Orders by Shift
**Classify transactions into Morning, Afternoon, and Evening shifts and count orders per shift.**

```sql
WITH hourly_sale AS (
    SELECT *,
        CASE
            WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
            WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
            ELSE 'Evening'
        END AS shift
    FROM retail_sales
)
SELECT 
    shift,
    COUNT(*) AS total_orders
FROM hourly_sale
GROUP BY shift;
```

---

## 🔍 Key SQL Concepts Used

- Data Cleaning with `IS NULL` checks and `DELETE`
- Aggregate functions: `SUM()`, `COUNT()`, `AVG()`, `ROUND()`
- `GROUP BY` for category and gender-level analysis
- `TO_CHAR()` for date formatting and filtering
- `EXTRACT()` for year, month, and hour extraction
- Window functions: `RANK()` with `PARTITION BY`
- Common Table Expressions (CTEs) — `WITH` clause
- `CASE WHEN` for conditional shift classification
- `LIMIT` for top-N queries

---

##  Key Findings

- Dataset contains **2,000 transactions** across **3 categories**: Clothing, Beauty, and Electronics
- Sales data spans **2 years**: 2022 and 2023
- Customers are segmented by **gender** (Male/Female) and **age**
- Transactions are distributed across **3 time shifts**: Morning, Afternoon, and Evening
- High-value transactions (above ₹1000) identified for premium customer targeting

---

## Project Structure

```
retail-sales-sql-analysis/
│
├── retails_sales.sql                      # All SQL queries
├── SQL_-_Retail_Sales_Analysis_utf_.csv   # Raw sales dataset
└── README.md                              # Project documentation
```

---

##  Tools Used

- **PostgreSQL** — Database & query execution
- **SQL** — Data cleaning, exploration, and analysis

---

##  How to Run

1. Set up a PostgreSQL database
2. Run the `CREATE TABLE` statement from `retails_sales.sql`
3. Import `SQL_-_Retail_Sales_Analysis_utf_.csv` into the `retailsales` table
4. Run the data cleaning queries
5. Execute the analysis queries one by one

---

##  Author

**RANU CHOUDHARY**  
Data Analyst Trainee  
📧 choudharyranu54@gmail.com  | 🔗 [[LinkedIn](https://www.linkedin.com/in/ranu-choudhary-36aa6a325?utm_source=share_via&utm_content=profile&utm_medium=member_android)] | 💻 [https://github.com/ranu-analytics/Data-Analyst-Portfolio]

---

> *This is a practice project built to strengthen SQL skills through real-world retail data analysis.*
