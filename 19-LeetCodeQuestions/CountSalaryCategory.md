# Count Salary Categories

## Problem Description

### Table: Accounts

| Column Name | Type | Description |
|-------------|------|-------------|
| account_id  | int  | Primary key |
| income      | int  | Monthly income in dollars |

## Task

Count the number of bank accounts in each salary category:
- "Low Salary": income < 20,000
- "Average Salary": 20,000 ≤ income ≤ 50,000
- "High Salary": income > 50,000

The result must include all categories, even if they have 0 accounts.

## Example

### Input:

**Accounts table:**
| account_id | income |
|------------|--------|
| 3          | 108939 |
| 2          | 12747  |
| 8          | 87709  |
| 6          | 91796  |

### Output:
| category       | accounts_count |
|----------------|----------------|
| Low Salary     | 1              |
| Average Salary | 0              |
| High Salary    | 3              |

### Explanation:
- Low Salary: 1 account (ID 2 with $12,747)
- Average Salary: 0 accounts
- High Salary: 3 accounts (IDs 3, 6, 8)

## Solution:

### Using UNION ALL with Categories:
```sql
SELECT 
    'Low Salary' AS category,
    COUNT(CASE WHEN income < 20000 THEN 1 END) AS accounts_count
FROM 
    Accounts
    
UNION ALL

SELECT 
    'Average Salary' AS category,
    COUNT(CASE WHEN income BETWEEN 20000 AND 50000 THEN 1 END) AS accounts_count
FROM 
    Accounts
    
UNION ALL

SELECT 
    'High Salary' AS category,
    COUNT(CASE WHEN income > 50000 THEN 1 END) AS accounts_count
FROM 
    Accounts;

```

```sql

WITH SalaryCounts AS (
    SELECT 
        SUM(CASE WHEN income > 50000 THEN 1 ELSE 0 END) AS High_Salary,
        SUM(CASE WHEN income BETWEEN 20000 AND 50000 THEN 1 ELSE 0 END) AS Average_Salary,
        SUM(CASE WHEN income < 20000 THEN 1 ELSE 0 END) AS Low_Salary
    FROM Accounts
)

SELECT  
    'Low Salary' AS category,
    Low_Salary AS accounts_count
FROM SalaryCounts 
UNION ALL
SELECT  
    'Average Salary' AS category,
    Average_Salary AS accounts_count
FROM SalaryCounts 
UNION ALL
SELECT  
    'High Salary' AS category,
    High_Salary AS accounts_count
FROM SalaryCounts;

```