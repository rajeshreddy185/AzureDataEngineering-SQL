# Replace Employee ID With The Unique Identifier

## Problem Description

### Tables

#### Employees
| Column Name | Type    |
|-------------|---------|
| id          | int     |
| name        | varchar |

- `id` is the primary key (column with unique values) for this table.
- Each row contains the id and name of an employee.

#### EmployeeUNI
| Column Name | Type |
|-------------|------|
| id          | int  |
| unique_id   | int  |

- `(id, unique_id)` is the composite primary key for this table.
- Each row contains an employee's id and their corresponding unique identifier.

## Task

Write a solution to show the unique ID of each employee. If an employee doesn't have a unique ID, show `null` instead.

Return the result table in any order.

## Example

### Input:

**Employees table:**
| id | name     |
|----|----------|
| 1  | Alice    |
| 7  | Bob      |
| 11 | Meir     |
| 90 | Winston  |
| 3  | Jonathan |

**EmployeeUNI table:**
| id | unique_id |
|----|-----------|
| 3  | 1         |
| 11 | 2         |
| 90 | 3         |

### Output:
| unique_id | name     |
|-----------|----------|
| null      | Alice    |
| null      | Bob      |
| 2         | Meir     |
| 3         | Winston  |
| 1         | Jonathan |

### Explanation:
- Alice and Bob don't have entries in the EmployeeUNI table, so their unique_id is shown as null.
- Meir (id: 11) has a unique_id of 2.
- Winston (id: 90) has a unique_id of 3.
- Jonathan (id: 3) has a unique_id of 1.

### Solution:

```sql
SELECT 
    eu.unique_id,
    e.name
FROM 
    Employees e
LEFT JOIN 
    EmployeeUNI eu ON e.id = eu.id
ORDER BY 
    e.name;