# Odd and Even Transactions

## Problem Statement

### Table: transactions

| Column Name      | Type | Description |
|------------------|------|-------------|
| transaction_id   | int  | Unique identifier for each transaction |
| amount           | int  | Transaction amount |
| transaction_date | date | Date of the transaction |

### Key Points:
- `transaction_id` is the primary key
- Each row represents a single transaction
- Need to categorize transactions as odd or even based on `transaction_id`
- Must include all dates, even if no odd/even transactions exist

## Task

For each transaction date, calculate:
1. The sum of amounts for odd `transaction_id`s (`odd_sum`)
2. The sum of amounts for even `transaction_id`s (`even_sum`)

If there are no transactions of a particular type (odd/even) for a date, display 0 for that sum.

## Example

### Input

**transactions table:**
| transaction_id | amount | transaction_date |
|----------------|--------|------------------|
| 1              | 150    | 2024-07-01       |
| 2              | 200    | 2024-07-01       |
| 3              | 75     | 2024-07-01       |
| 4              | 300    | 2024-07-02       |
| 5              | 50     | 2024-07-02       |
| 6              | 120    | 2024-07-03       |

### Output
| transaction_date | odd_sum | even_sum |
|------------------|---------|----------|
| 2024-07-01       | 75      | 350      |
| 2024-07-02       | 0       | 350      |
| 2024-07-03       | 0       | 120      |

## Solution

```sql
SELECT 
    transaction_date,
    COALESCE(SUM(CASE WHEN transaction_id % 2 = 1 THEN amount ELSE 0 END), 0) AS odd_sum,
    COALESCE(SUM(CASE WHEN transaction_id % 2 = 0 THEN amount ELSE 0 END), 0) AS even_sum
FROM 
    transactions
GROUP BY 
    transaction_date
ORDER BY 
    transaction_date;
```

