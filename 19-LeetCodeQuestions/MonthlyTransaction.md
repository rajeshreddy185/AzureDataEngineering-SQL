# Monthly Transactions

## Problem Statement

### Table: Transactions

| Column Name | Type    | Description |
|-------------|---------|-------------|
| id          | int     | Primary Key |
| country     | varchar | Country code |
| state       | enum    | Transaction state: "approved" or "declined" |
| amount      | int     | Transaction amount |
| trans_date  | date    | Transaction date |

## Task

For each month and country, calculate:
1. Total number of transactions
2. Total transaction amount
3. Number of approved transactions
4. Total amount of approved transactions

### Requirements:
- Group by month (YYYY-MM) and country
- Order doesn't matter
- Include all months with transactions

## Example

### Input

**Transactions table:**
| id  | country | state    | amount | trans_date |
|-----|---------|----------|--------|------------|
| 121 | US      | approved | 1000   | 2018-12-18 |
| 122 | US      | declined | 2000   | 2018-12-19 |
| 123 | US      | approved | 2000   | 2019-01-01 |
| 124 | DE      | approved | 2000   | 2019-01-07 |

### Output
| month   | country | trans_count | approved_count | trans_total_amount | approved_total_amount |
|---------|---------|-------------|----------------|--------------------|-----------------------|
| 2018-12 | US      | 2           | 1              | 3000               | 1000                  |
| 2019-01 | US      | 1           | 1              | 2000               | 2000                  |
| 2019-01 | DE      | 1           | 1              | 2000               | 2000                  |

## Solution

```sql
SELECT 
    DATE_FORMAT(trans_date, '%Y-%m') AS month,
    country,
    COUNT(*) AS trans_count,
    SUM(CASE WHEN state = 'approved' THEN 1 ELSE 0 END) AS approved_count,
    SUM(amount) AS trans_total_amount,
    SUM(CASE WHEN state = 'approved' THEN amount ELSE 0 END) AS approved_total_amount
FROM 
    Transactions
GROUP BY 
    DATE_FORMAT(trans_date, '%Y-%m'),
    country
ORDER BY 
    month, 
    country;
```


```sql 
SELECT CONCAT(YEAR(trans_date), '-', LPAD(MONTH(trans_date), 2, 0)) as `month`,
       country,
       CONCAT(id) AS trans_count,
       SUM(CASE WHEN state='approved' THEN 1 ELSE 0 END) AS approved_count,
       SUM(amount) as trans_total_amount,
       SUM(CASE WHEN STATE='approved' THEN amount ELSE 0 END) as approved_total_amount
FROM
    Transactions
GROUP BY YEAR(trans_date), MONTH(trans_date), country
```