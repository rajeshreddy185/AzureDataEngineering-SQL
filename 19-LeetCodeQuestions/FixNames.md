# Fix Names Format

## Problem Description

### Table: Users

| Column Name | Type    |
|-------------|---------|
| user_id     | int     |
| name        | varchar |

- `user_id` is the primary key (column with unique values) for this table.
- `name` contains only lowercase and uppercase English letters.

## Task

Write a solution to format the `name` column so that:
- The first character is uppercase
- All other characters are lowercase

Return the result ordered by `user_id`.

## Example

### Input:

**Users table:**
| user_id | name  |
|---------|-------|
| 1       | aLice |
| 2       | bOB   |

### Output:
| user_id | name  |
|---------|-------|
| 1       | Alice |
| 2       | Bob   |

### Solution:

```sql
SELECT 
    user_id,
    CONCAT(
        UPPER(SUBSTRING(name, 1, 1)),
        LOWER(SUBSTRING(name, 2))
    ) AS name
FROM 
    Users
ORDER BY 
    user_id;