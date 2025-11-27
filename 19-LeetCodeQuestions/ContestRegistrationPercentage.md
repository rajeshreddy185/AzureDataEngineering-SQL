# Contest Registration Percentage

## Problem Description

### Tables

#### Users
| Column Name | Type    |
|-------------|---------|
| user_id     | int     |
| user_name   | varchar |

- `user_id` is the primary key (column with unique values) for this table.
- Each row contains a user's ID and name.

#### Register
| Column Name | Type |
|-------------|------|
| contest_id  | int  |
| user_id     | int  |

- `(contest_id, user_id)` is the composite primary key for this table.
- Each row represents a user's registration for a contest.

## Task

Calculate the percentage of users registered in each contest, rounded to two decimal places.

Return the result ordered by:
1. `percentage` in descending order
2. `contest_id` in ascending order (in case of ties)

## Example

### Input:

**Users table:**
| user_id | user_name |
|---------|-----------|
| 6       | Alice     |
| 2       | Bob       |
| 7       | Alex      |

**Register table:**
| contest_id | user_id |
|------------|---------|
| 215        | 6       |
| 209        | 2       |
| 208        | 2       |
| 210        | 6       |
| 208        | 6       |
| 209        | 7       |
| 209        | 6       |
| 215        | 7       |
| 208        | 7       |
| 210        | 2       |
| 207        | 2       |
| 210        | 7       |

### Output:
| contest_id | percentage |
|------------|------------|
| 208        | 100.00     |
| 209        | 100.00     |
| 210        | 100.00     |
| 215        | 66.67      |
| 207        | 33.33      |

### Explanation:
- Contests 208, 209, and 210: All 3 users registered (100%)
- Contest 215: 2 out of 3 users registered (66.67%)
- Contest 207: 1 out of 3 users registered (33.33%)

### Solution:

```sql
SELECT 
    r.contest_id,
    ROUND(
        (COUNT(DISTINCT r.user_id) * 100.0) / 
        (SELECT COUNT(*) FROM Users), 
        2
    ) AS percentage
FROM 
    Register r
GROUP BY 
    r.contest_id
ORDER BY 
    percentage DESC,
    r.contest_id ASC;