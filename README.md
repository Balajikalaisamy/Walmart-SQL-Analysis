# Walmart SQL Analysis Project

## Overview
This project leverages Python, MySQL, and Kaggle to perform a detailed analysis of transaction data from Walmart. The main objectives are to explore sales trends, branch performance, customer preferences, and profit margins, using SQL queries and Python for data manipulation and analysis.

## Tools Used
- **Python**: For data preprocessing, analysis, and integration with the MySQL database.
- **MySQL**: To store, query, and manage the Walmart dataset.
- **Kaggle**: Source of the dataset for analysis and insights.

## Features
This repository includes SQL queries and Python scripts for:
- **Database Insights**: Total transactions, day-wise trends, and branch-specific sales analysis.
- **Shift-Based Analysis**: Understand transaction patterns during different times of the day (Morning, Afternoon, Evening).
- **Top Categories**: Identify high-performing categories by average ratings.
- **Yearly Profit Analysis**: Compare profit margins between 2024 and 2025 with detailed calculations.
- **Payment Method Trends**: Explore sales patterns by payment method.

## Queries and Insights

### 1. Basic Data Exploration
- Retrieve all records and count the total number of rows in the database.
```sql
SELECT COUNT(*) FROM walmart;
### 2. **Sales Analysis by Payment Method**
-  Sales Analysis by Payment Method
  ```sql
-  SELECT 
    payment_method AS Payment_Method,
    SUM(quantity) AS No_of_qty_sold,
    COUNT(*) AS No_of_Transactions
FROM walmart
GROUP BY payment_method;
```
### 3.**Top-Rated Categories**
- Use ranking to identify the highest-rated category for each branch.
```Sql
SELECT * 
FROM (
    SELECT 
        branch, 
        category, 
        AVG(rating) AS Avg_Rating,
        RANK() OVER (PARTITION BY branch ORDER BY AVG(rating) DESC) AS Rank_rating
    FROM walmart
    GROUP BY category, branch
) AS temp 
WHERE Rank_rating = 1;
```
### 4.**Yearly Profit Comparison**
- Calculate and compare profits for 2024 and 2025:
```SQL
WITH year_2022 AS (
    SELECT 
        branch, 
        ROUND(SUM(total_amt * profit_margin), 2) AS year_2022
    FROM walmart
    WHERE YEAR(date) = 2024
    GROUP BY branch
),
year_2023 AS (
    SELECT 
        branch, 
        ROUND(SUM(total_amt * profit_margin), 2) AS year_2023
    FROM walmart
    WHERE YEAR(date) = 2025
    GROUP BY branch
)
SELECT 
    ls.year_2022,
    ls.branch,
    cs.year_2023,
    ROUND((ls.year_2022 - cs.year_2023) / ls.year_2022 * 100, 2) AS ratio
FROM year_2022 AS ls
JOIN year_2023 AS cs
    ON ls.branch = cs.branch
WHERE ls.year_2022 > cs.year_2023
ORDER BY ratio DESC
LIMIT 5;
````
### **5. Shift-Based Transactions**
- Categorize transactions into Morning, Afternoon, and Evening shifts by branch:
```SQL
SELECT 
    CASE 
        WHEN HOUR(time) < 12 THEN 'Morning'
        WHEN HOUR(time) BETWEEN 12 AND 17 THEN 'Afternoon'
        ELSE 'Evening'
    END AS shift,
    COUNT(*) AS total_transactions,
    branch
FROM walmart
GROUP BY shift, branch
ORDER BY branch, total_transactions;
---
