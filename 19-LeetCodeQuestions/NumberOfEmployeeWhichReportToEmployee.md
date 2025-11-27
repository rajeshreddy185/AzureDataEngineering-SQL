# Managers with Direct Reports

## Problem Description

### Table: Employees

| Column Name | Type    |
|-------------|---------|
| employee_id | int     |
| name        | varchar |
| reports_to  | int     |
| age         | int     |

- `employee_id` is the primary key (column with unique values) for this table.
- `reports_to` contains the employee_id of the manager (can be NULL for top-level employees).

## Task

For each manager (employees who have at least one direct report), find:
1. Their employee ID and name
2. The number of direct reports
3. The average age of their direct reports (rounded to the nearest integer)

Order the result by `employee_id` in ascending order.

## Examples

### Example 1:

**Input:**
| employee_id | name    | reports_to | age |
|-------------|---------|------------|-----|
| 9           | Hercy   | null       | 43  |
| 6           | Alice   | 9          | 41  |
| 4           | Bob     | 9          | 36  |
| 2           | Winston | null       | 37  |

**Output:**
| employee_id | name  | reports_count | average_age |
|-------------|-------|---------------|-------------|
| 9           | Hercy | 2             | 39          |

### Example 2:

**Input:**
| employee_id | name    | reports_to | age |
|-------------|---------|------------|-----|
| 1           | Michael | null       | 45  |
| 2           | Alice   | 1          | 38  |
| 3           | Bob     | 1          | 42  |
| 4           | Charlie | 2          | 34  |
| 5           | David   | 2          | 40  |
| 6           | Eve     | 3          | 37  |
| 7           | Frank   | null       | 50  |
| 8           | Grace   | null       | 48  |

**Output:**
| employee_id | name    | reports_count | average_age |
|-------------|---------|---------------|-------------|
| 1           | Michael | 2             | 40          |
| 2           | Alice   | 2             | 37          |
| 3           | Bob     | 1             | 37          |

## Solution:

```sql
SELECT 
    m.employee_id,
    m.name,
    COUNT(e.employee_id) AS reports_count,
    ROUND(AVG(e.age), 0) AS average_age
FROM 
    Employees e
JOIN 
    Employees m ON e.reports_to = m.employee_id
GROUP BY 
    m.employee_id, m.name
HAVING 
    COUNT(e.employee_id) >= 1
ORDER BY 
    m.employee_id;