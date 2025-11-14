# Employees Earning More Than Their Managers

## Problem Statement

Table: Employee

| Column Name | Type    | Description |
|-------------|---------|-------------|
| id          | int     | Primary key |
| name        | varchar | Employee name|
| salary      | int     | Employee salary|
| managerId   | int     | Foreign key referencing Employee(id) |

id is the primary key (column with unique values) for this table.
Each row of this table indicates the ID of an employee, their name, salary, and the ID of their manager.

Write a solution to find the employees who earn more than their managers.

Return the result table in any order.

## Example

### Input

**Employee Table:**
| id | name  | salary | managerId |
|----|-------|--------|-----------|
| 1  | Joe   | 70000  | 3         |
| 2  | Henry | 80000  | 4         |
| 3  | Sam   | 60000  | Null      |
| 4  | Max   | 90000  | Null      |

### Expected Output
| Employee |
|----------|
| Joe      |

**Explanation:** Joe is the only employee who earns more than his manager.

## Solutions

### Solution 1: Self Join

```sql
SELECT e1.name FROM Employee as e1 
JOIN Employee as e2
ON e1.id = e2.managerId
WHERE e1.salary > e2.salary;
```

### Solution 2: Using EXISTS

```sql
SELECT e1.name
FROM Employee AS e1 -- Outer Query: The potential Manager
WHERE EXISTS (
    SELECT 1 
    FROM Employee AS e2 -- Inner Query: The Employee being checked
    WHERE e2.managerId = e1.id       -- Correlation: links the employee to the outer manager
    AND e1.salary > e2.salary        -- Condition: manager earns more
);
```

## JOIN Output Table

This shows the result of joining the Employee table with itself to find manager-employee relationships:

| id | name  | salary | managerId | id | name  | salary | managerId |
|----|-------|--------|-----------|----|-------|--------|-----------|
| 1  | Joe   | 70000  | 3         | 3  | Sam   | 60000  | Null      |
| 2  | Henry | 80000  | 4         | 4  | Max   | 90000  | Null      |
| 3  | Sam   | 60000  | Null      |    |       |        |           |
| 4  | Max   | 90000  | Null      |    |       |        |           |

### Explanation of the JOIN Output:
- The first four columns represent the employee (e1)
- The last four columns represent their manager (e2)
- Rows where managerId is NULL don't have a matching manager in the join
- The condition `e1.salary > e2.salary` in the WHERE clause filters for employees who earn more than their managers