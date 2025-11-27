# Bank Account Summary

## Problem Description

### Tables

#### Users
| Column Name | Type    |
|-------------|---------|
| account     | int     |
| name        | varchar |

- `account` is the primary key (column with unique values) for this table.
- Each row contains a user's account number and name.
- No two users have the same name.

#### Transactions
| Column Name   | Type |
|---------------|------|
| trans_id      | int  |
| account       | int  |
| amount        | int  |
| transacted_on | date |

- `trans_id` is the primary key (column with unique values) for this table.
- Each row represents a transaction for an account.
- `amount` is positive for deposits and negative for withdrawals.
- All accounts start with a balance of 0.

## Task

Write a solution to find users with an account balance greater than 10,000. The balance is calculated as the sum of all transaction amounts for each account.

Return the result in any order.

## Example

### Input:

**Users table:**
| account | name   |
|---------|--------|
| 900001  | Alice  |
| 900002  | Bob    |
| 900003  | Charlie|

**Transactions table:**
| trans_id | account | amount | transacted_on |
|----------|---------|--------|---------------|
| 1        | 900001  | 7000   | 2020-08-01    |
| 2        | 900001  | 7000   | 2020-09-01    |
| 3        | 900001  | -3000  | 2020-09-02    |
| 4        | 900002  | 1000   | 2020-09-12    |
| 5        | 900003  | 6000   | 2020-08-07    |
| 6        | 900003  | 6000   | 2020-09-07    |
| 7        | 900003  | -4000  | 2020-09-11    |

### Output:
| name  | balance |
|-------|---------|
| Alice | 11000   |

### Explanation:
- Alice's balance: 7000 + 7000 - 3000 = 11000
- Bob's balance: 1000
- Charlie's balance: 6000 + 6000 - 4000 = 8000
- Only Alice has a balance > 10000

### Solution:

```sql
SELECT 
    u.name,
    SUM(t.amount) AS balance
FROM 
    Users u
JOIN 
    Transactions t ON u.account = t.account
GROUP BY 
    u.account, u.name
HAVING 
    SUM(t.amount) > 10000
ORDER BY 
    balance DESC;