# Primary Department for Each Employee

## Problem Description

### Table: Employee

| Column Name   | Type    | Description |
|---------------|---------|-------------|
| employee_id   | int     | Employee identifier |
| department_id | int     | Department identifier |
| primary_flag  | enum    | 'Y' if this is the primary department, 'N' otherwise |

- An employee can belong to multiple departments.
- When an employee belongs to only one department, that is their primary department.
- When an employee belongs to multiple departments, the one marked with `primary_flag = 'Y'` is their primary department.

## Task

For each employee, find their primary department. Return the result table in any order.

## Example

### Input:

**Employee table:**
| employee_id | department_id | primary_flag |
|-------------|---------------|--------------|
| 1           | 1             | N            |
| 2           | 1             | Y            |
| 2           | 2             | N            |
| 3           | 3             | N            |
| 4           | 2             | N            |
| 4           | 3             | Y            |
| 4           | 4             | N            |

### Output:
| employee_id | department_id |
|-------------|---------------|
| 1           | 1             |
| 2           | 1             |
| 3           | 3             |
| 4           | 3             |

### Explanation:
- Employee 1: Only one department (1) → Primary department is 1
- Employee 2: Two departments, one marked as primary (1) → Primary department is 1
- Employee 3: Only one department (3) → Primary department is 3
- Employee 4: Three departments, one marked as primary (3) → Primary department is 3

## Solution:

### Using UNION approach:
```sql
-- For employees with only one department
SELECT employee_id, department_id
FROM Employee
WHERE employee_id IN (
    SELECT employee_id
    FROM Employee
    GROUP BY employee_id
    HAVING COUNT(*) = 1
)
UNION
-- For employees with multiple departments, get the one marked as primary
SELECT employee_id, department_id
FROM Employee
WHERE primary_flag = 'Y'
ORDER BY employee_id;
```
```sql
SELECT 
    employee_id,
    CASE 
        WHEN COUNT(*) = 1 THEN MAX(department_id)
        ELSE MAX(CASE WHEN primary_flag = 'Y' THEN department_id END)
    END AS department_id
FROM 
    Employee
GROUP BY 
    employee_id
ORDER BY 
    employee_id;
```


```sql
select employee_id,  department_id
from Employee 
where primary_flag ='Y'
or employee_id in (
    select employee_id from Employee group by employee_id having(count(employee_id)=1)
)

```
