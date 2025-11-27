# Confirmation Rate

## Problem Description

### Table: Signups

| Column Name | Type     | Description |
|-------------|----------|-------------|
| user_id     | int      | Primary key |
| time_stamp  | datetime | Signup time |

### Table: Confirmations

| Column Name | Type     | Description |
|-------------|----------|-------------|
| user_id     | int      | Foreign key to Signups |
| time_stamp  | datetime | Confirmation timestamp |
| action      | ENUM     | 'confirmed' or 'timeout' |

## Task

Calculate the confirmation rate for each user, defined as:
- Number of 'confirmed' messages ÷ Total number of requested confirmations
- 0 if no confirmations were requested
- Round to 2 decimal places

## Example

### Input:

**Signups table:**
| user_id | time_stamp          |
|---------|---------------------|
| 3       | 2020-03-21 10:16:13 |
| 7       | 2020-01-04 13:57:59 |
| 2       | 2020-07-29 23:09:44 |
| 6       | 2020-12-09 10:39:37 |

**Confirmations table:**
| user_id | time_stamp          | action    |
|---------|---------------------|-----------|
| 3       | 2021-01-06 03:30:46 | timeout   |
| 3       | 2021-07-14 14:00:00 | timeout   |
| 7       | 2021-06-12 11:57:29 | confirmed |
| 7       | 2021-06-13 12:58:28 | confirmed |
| 7       | 2021-06-14 13:59:27 | confirmed |
| 2       | 2021-01-22 00:00:00 | confirmed |
| 2       | 2021-02-28 23:59:59 | timeout   |

### Output:
| user_id | confirmation_rate |
|---------|-------------------|
| 6       | 0.00              |
| 3       | 0.00              |
| 7       | 1.00              |
| 2       | 0.50              |

## Solution:

### Using LEFT JOIN and AVG:
```sql
SELECT 
    s.user_id,
    ROUND(
        COALESCE(
            AVG(CASE WHEN c.action = 'confirmed' THEN 1.0 ELSE 0.0 END),
            0
        ),
        2
    ) AS confirmation_rate
FROM 
    Signups s
LEFT JOIN 
    Confirmations c ON s.user_id = c.user_id
GROUP BY 
    s.user_id
ORDER BY 
    s.user_id;

```


```sql

WITH action_counts AS (
    SELECT 
        user_id, 
        SUM(CASE WHEN action = 'timeout' THEN 1 ELSE 0 END) AS timeout_count,
        SUM(CASE WHEN action = 'confirmed' THEN 1 ELSE 0 END) AS confirmed_count
    FROM 
        Confirmations
    GROUP BY 
        user_id
)
SELECT 
    S.user_id, 
    ROUND(
        COALESCE(
            A.confirmed_count * 1.0 / 
            NULLIF((A.confirmed_count + A.timeout_count), 0),
            0
        ), 
        2
    ) AS confirmation_rate
FROM 
    Signups AS S
LEFT JOIN 
    action_counts AS A ON S.user_id = A.user_id
ORDER BY 
    S.user_id;


```