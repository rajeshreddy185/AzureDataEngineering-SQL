# Latest Login in 2020

## Problem Description

### Table: Logins

| Column Name | Type     | Description |
|-------------|----------|-------------|
| user_id     | int      | User identifier |
| time_stamp  | datetime | Login timestamp |

- `(user_id, time_stamp)` is the primary key
- Each row represents a login event for a user

## Task

Find the most recent login timestamp in 2020 for each user who logged in at least once during 2020.

## Example

### Input:

**Logins table:**
| user_id | time_stamp          |
|---------|---------------------|
| 6       | 2020-06-30 15:06:07 |
| 6       | 2021-04-21 14:06:06 |
| 6       | 2019-03-07 00:18:15 |
| 8       | 2020-02-01 05:10:53 |
| 8       | 2020-12-30 00:46:50 |
| 2       | 2020-01-16 02:49:50 |
| 2       | 2019-08-25 07:59:08 |
| 14      | 2019-07-14 09:00:00 |
| 14      | 2021-01-06 11:59:59 |

### Output:
| user_id | last_stamp          |
|---------|---------------------|
| 6       | 2020-06-30 15:06:07 |
| 8       | 2020-12-30 00:46:50 |
| 2       | 2020-01-16 02:49:50 |

### Explanation:
- User 6: Only one login in 2020
- User 8: Two logins in 2020, latest is December 30
- User 2: Only one login in 2020
- User 14: No logins in 2020 (excluded)

## Solution:

### Using GROUP BY with MAX:
```sql
SELECT 
    user_id,
    MAX(time_stamp) AS last_stamp
FROM 
    Logins
WHERE 
    time_stamp BETWEEN '2020-01-01 00:00:00' AND '2020-12-31 23:59:59'
GROUP BY 
    user_id
ORDER BY 
    user_id;
```

```sql
SELECT 
    user_id,
    MAX(time_stamp) AS last_stamp
FROM 
    Logins
WHERE 
    YEAR(time_stamp) = 2020
GROUP BY 
    user_id
ORDER BY 
    user_id;


```