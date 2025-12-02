# Find Loyal Customers

## Problem Statement

### Table: customer_transactions

| Column Name      | Type    | Description |
|------------------|---------|-------------|
| transaction_id   | int     | Unique identifier for each transaction |
| customer_id      | int     | Identifier for the customer |
| transaction_date | date    | Date of the transaction |
| amount           | decimal | Transaction amount |
| transaction_type | varchar | Either 'purchase' or 'refund' |

### Key Points:
A customer is considered **loyal** if they meet ALL the following criteria:
1. Made **at least 3 purchase transactions**
2. Have been **active for at least 30 days** (from first to last transaction)
3. Have a **refund rate less than 20%**
   - Refund rate = (number of refunds) / (total number of transactions)
   - Total transactions = purchases + refunds

### Output:
- Return only the `customer_id` of loyal customers
- Order the result by `customer_id` in ascending order

## Example

### Input:

**customer_transactions table:**
| transaction_id | customer_id | transaction_date | amount | transaction_type |
|----------------|-------------|------------------|--------|------------------|
| 1              | 101         | 2024-01-05       | 150.00 | purchase         |
| 2              | 101         | 2024-01-15       | 200.00 | purchase         |
| 3              | 101         | 2024-02-10       | 180.00 | purchase         |
| 4              | 101         | 2024-02-20       | 250.00 | purchase         |
| 5              | 102         | 2024-01-10       | 100.00 | purchase         |
| 6              | 102         | 2024-01-12       | 120.00 | purchase         |
| 7              | 102         | 2024-01-15       | 80.00  | refund           |
| 8              | 102         | 2024-01-18       | 90.00  | refund           |
| 9              | 102         | 2024-02-15       | 130.00 | purchase         |
| 10             | 103         | 2024-01-01       | 500.00 | purchase         |
| 11             | 103         | 2024-01-02       | 450.00 | purchase         |
| 12             | 103         | 2024-01-03       | 400.00 | purchase         |
| 13             | 104         | 2024-01-01       | 200.00 | purchase         |
| 14             | 104         | 2024-02-01       | 250.00 | purchase         |
| 15             | 104         | 2024-02-15       | 300.00 | purchase         |
| 16             | 104         | 2024-03-01       | 350.00 | purchase         |
| 17             | 104         | 2024-03-10       | 280.00 | purchase         |
| 18             | 104         | 2024-03-15       | 100.00 | refund           |

### Output:
| customer_id |
|-------------|
| 101         |
| 104         |

## Solution

```sql
WITH customer_metrics AS (
    SELECT 
        customer_id,
        COUNT(*) AS total_transactions,
        SUM(CASE WHEN transaction_type = 'purchase' THEN 1 ELSE 0 END) AS purchase_count,
        SUM(CASE WHEN transaction_type = 'refund' THEN 1 ELSE 0 END) AS refund_count,
        DATEDIFF(MAX(transaction_date), MIN(transaction_date)) AS active_days
    FROM 
        customer_transactions
    GROUP BY 
        customer_id
    HAVING 
        SUM(CASE WHEN transaction_type = 'purchase' THEN 1 ELSE 0 END) >= 3
        AND DATEDIFF(MAX(transaction_date), MIN(transaction_date)) >= 30
)
SELECT 
    customer_id
FROM 
    customer_metrics
WHERE 
    refund_count * 1.0 / total_transactions < 0.2
ORDER BY 
    customer_id;
```


```sql
select customer_id
from customer_transactions
group by customer_id
having count(transaction_id) >= 3 and
datediff(max(transaction_date) ,min(transaction_date)) >= 30 and
(sum(case when transaction_type = 'refund' then 1 else 0 end)* 100 /
sum(case when transaction_type = 'purchase' then 1 else 0 end)) <= 20

```