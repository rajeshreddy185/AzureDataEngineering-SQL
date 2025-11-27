# Calculate Special Bonus

## Problem Description

### Table: Employees

| Column Name | Type    | Description |
|-------------|---------|-------------|
| employee_id | int     | Primary key |
| name        | varchar | Employee name |
| salary      | int     | Employee salary |

## Task

Calculate the bonus for each employee based on these rules:
- If the employee's ID is odd AND their name does NOT start with 'M', bonus = 100% of salary
- Otherwise, bonus = 0

Return the result ordered by `employee_id`.

## Example

### Input:

**Employees table:**
| employee_id | name    | salary |
|-------------|---------|--------|
| 2           | Meir    | 3000   |
| 3           | Michael | 3800   |
| 7           | Addilyn | 7400   |
| 8           | Juan    | 6100   |
| 9           | Kannon  | 7700   |

### Output:
| employee_id | bonus |
|-------------|-------|
| 2           | 0     |
| 3           | 0     |
| 7           | 7400  |
| 8           | 0     |
| 9           | 7700  |

### Explanation:
- Employee 2: Even ID → 0 bonus
- Employee 3: Name starts with 'M' → 0 bonus
- Employee 7: Meets both conditions → 100% of 7400 = 7400
- Employee 8: Even ID → 0 bonus
- Employee 9: Meets both conditions → 100% of 7700 = 7700

## Solution:

### Using CASE Expression:

```sql
-- Cleaned and optimized version
SELECT 
    employee_id,
    salary AS bonus
FROM 
    Employees
WHERE 
    employee_id % 2 = 1 
    AND name NOT LIKE 'M%'
    
UNION ALL

SELECT 
    employee_id,
    0 AS bonus
FROM 
    Employees
WHERE 
    employee_id % 2 = 0
    OR name LIKE 'M%'
ORDER BY 
    employee_id;
```


```sql
SELECT 
    employee_id,
    CASE 
        WHEN employee_id % 2 = 1 AND name NOT LIKE 'M%' THEN salary
        ELSE 0 
    END AS bonus
FROM 
    Employees
ORDER BY 
    employee_id;