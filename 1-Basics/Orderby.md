**  ORDER BY ** (Sorting Results)
The ORDER BY clause is used to sort the result set of a query by one or more specified columns.

Syntax: Must be the last clause in a query (before LIMIT).

Direction:

ASC (Ascending): Sorts from lowest to highest (A-Z, 0-9, oldest to newest). This is the default if no direction is specified.
DESC (Descending): Sorts from highest to lowest (Z-A, 9-0, newest to oldest).

Multi-Column Sort: You can sort by multiple columns; the second column acts as a tie-breaker for rows that have the same value in the first column.

Example: Find the top 10 highest-paid employees.

SQL
```
SELECT
    EmployeeName,
    Salary
FROM
    Employees
ORDER BY
    Salary DESC, -- Primary sort: Highest salary first
    EmployeeName ASC; -- Secondary sort: Alphabetical name (A-Z) for ties

```