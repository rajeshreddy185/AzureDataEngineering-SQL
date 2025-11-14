Table: Employee

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| name        | varchar |
| salary      | int     |
| managerId   | int     |
+-------------+---------+

id is the primary key (column with unique values) for this table.
Each row of this table indicates the ID of an employee, their name, salary, and the ID of their manager.

Write a solution to find the employees who earn more than their managers.

Return the result table in any order.
The result format is in the following example.

Example 1:

Input: 
Employee table:
+----+-------+--------+-----------+
| id | name  | salary | managerId |
+----+-------+--------+-----------+
| 1  | Joe   | 70000  | 3         |
| 2  | Henry | 80000  | 4         |
| 3  | Sam   | 60000  | Null      |
| 4  | Max   | 90000  | Null      |
+----+-------+--------+-----------+
Output: 
+----------+
| Employee |
+----------+
| Joe      |
+----------+
Explanation: Joe is the only employee who earns more than his manager.


```commandline

SELECT e1.name FROM Employee as e1 JOIN Employee as e2
ON e1.id = e2.managerId
WHERE e1.salary > e2.salary;

```

JOIN OUTPUT TABLE:

+----+-------+--------+-----------++----+-------+--------+-----------+
| id | name  | salary | managerId || id | name  | salary | managerId |
+----+-------+--------+-----------++----+-------+--------+-----------+
| 1  | Joe   | 70000  | 3         || 3  | Sam   | 60000  | Null      |
| 2  | Henry | 80000  | 4         || 4  | Max   | 90000  | Null      |
| 3  | Sam   | 60000  | Null      |-----------------------------------
| 4  | Max   | 90000  | Null      |
+----+-------+--------+-----------+



```commandline

SELECT e1.name
FROM Employee AS e1 -- Outer Query: The potential Manager
WHERE EXISTS (
    SELECT 1 
    FROM Employee AS e2 -- Inner Query: The Employee being checked
    WHERE e2.managerId = e1.id       -- Correlation: links the employee to the outer manager
    AND e1.salary > e2.salary        -- Condition: manager earns more
);

```