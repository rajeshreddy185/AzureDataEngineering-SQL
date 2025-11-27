# Employees Whose Manager Left the Company

## Problem Description

### Table: Employees

| Column Name | Type    | Description |
|-------------|---------|-------------|
| employee_id | int     | Primary key |
| name        | varchar | Employee name |
| manager_id  | int     | ID of the manager (can be NULL) |
| salary      | int     | Employee salary |

## Task

Find employees who:
1. Earn strictly less than $30,000
2. Their manager has left the company (manager's ID doesn't exist in the table)
3. Exclude employees who don't have a manager (manager_id is NULL)

Return the result ordered by `employee_id`.

## Example

### Input:

**Employees table:**
| employee_id | name      | manager_id | salary |
|-------------|-----------|------------|--------|
| 3           | Mila      | 9          | 60301  |
| 12          | Antonella | null       | 31000  |
| 13          | Emery     | null       | 67084  |
| 1           | Kalel     | 11         | 21241  |
| 9           | Mikaela   | null       | 50937  |
| 11          | Joziah    | 6          | 28485  |

### Output:
| employee_id |
|-------------|
| 11          |

### Explanation:
- Employees with salary < $30,000: 1 (Kalel) and 11 (Joziah)
- Kalel's manager (11) is still in the company
- Joziah's manager (6) is not in the Employees table
- Only Joziah meets both conditions

## Solution:
``` sql
SELECT 
    employee_id
FROM 
    Employees
WHERE 
    salary < 30000
    AND manager_id IS NOT NULL
    AND manager_id NOT IN (
        SELECT employee_id 
        FROM Employees
    )
ORDER BY 
    employee_id;
```
### Using LEFT JOIN with NULL check:

```sql
SELECT 
    e.employee_id
FROM 
    Employees e
LEFT JOIN 
    Employees m ON e.manager_id = m.employee_id
WHERE 
    e.salary < 30000
    AND e.manager_id IS NOT NULL
    AND m.employee_id IS NULL
ORDER BY 
    e.employee_id;