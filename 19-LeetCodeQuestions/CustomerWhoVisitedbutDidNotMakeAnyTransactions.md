# Customers Who Visited But Did Not Make Any Transactions

## Problem Description

### Tables

#### Visits
| Column Name | Type |
|-------------|------|
| visit_id    | int  |
| customer_id | int  |

- `visit_id` is the column with unique values for this table.
- Contains information about customers who visited the mall.

#### Transactions
| Column Name    | Type |
|----------------|------|
| transaction_id | int  |
| visit_id       | int  |
| amount         | int  |

- `transaction_id` is the column with unique values.
- Contains transaction details for each visit.

## Task

Find the IDs of customers who visited the mall without making any transactions, along with the count of such visits.

Return the result in any order.

## Example

### Input:

**Visits table:**
| visit_id | customer_id |
|----------|-------------|
| 1        | 23          |
| 2        | 9           |
| 4        | 30          |
| 5        | 54          |
| 6        | 96          |
| 7        | 54          |
| 8        | 54          |

**Transactions table:**
| transaction_id | visit_id | amount |
|----------------|----------|--------|
| 2              | 5        | 310    |
| 3              | 5        | 300    |
| 9              | 5        | 200    |
| 12             | 1        | 910    |
| 13             | 2        | 970    |

### Output:
| customer_id | count_no_trans |
|-------------|----------------|
| 54          | 2              |
| 30          | 1              |
| 96          | 1              |

### Explanation:
- Customer 23 made a transaction during their visit.
- Customer 9 made a transaction during their visit.
- Customer 30 visited once without any transactions.
- Customer 54 visited three times: made transactions in one visit, no transactions in two visits.
- Customer 96 visited once without any transactions.

### Solution:

```sql
SELECT 
    v.customer_id,
    COUNT(v.visit_id) AS count_no_trans
FROM 
    Visits v
LEFT JOIN 
    Transactions t ON v.visit_id = t.visit_id
WHERE 
    t.transaction_id IS NULL
GROUP BY 
    v.customer_id
ORDER BY 
    count_no_trans DESC;