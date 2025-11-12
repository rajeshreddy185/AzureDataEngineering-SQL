** WITH  Common Table Expression (CTE) **

The SQL keyword WITH introduces a Common Table Expression (CTE). A CTE is a temporary, named result set that is 
defined within the execution scope of a single SELECT, INSERT, UPDATE, or DELETE statement.

It acts like a temporary, non-persisted view that you can reference multiple times within the query immediately 
following the WITH block.

```
like def in python
```


The WITH clause always precedes the main query and is defined using the following structure:

SQL
```
WITH
    CTE_Name_1 AS (
        -- The SELECT statement that defines the first temporary result set
        SELECT column1, column2
        FROM TableA
        WHERE condition
    ),
    CTE_Name_2 AS (
        -- The SELECT statement that defines the second temporary result set
        SELECT column_x, column_y
        FROM TableB
    )

-- The final query that uses one or more of the defined CTEs
SELECT *
FROM CTE_Name_1
JOIN CTE_Name_2 ON CTE_Name_1.id = CTE_Name_2.id;

```


Primary Reasons to Use WITH (CTEs)
The WITH clause is critical for improving query readability and enabling recursion.

1. Improved Readability and Organization 

CTEs allow you to break down a long, complex query (especially one involving multiple joins or subqueries) 
into smaller, logical, named steps. Instead of nesting subqueries deep within the FROM clause, 
you define them clearly at the top.

Before CTE (Nested Subquery):

SQL
```
SELECT C.City, A.AvgOrder
FROM Customers C
JOIN (
    SELECT CustomerID, AVG(Total) AS AvgOrder
    FROM Orders
    GROUP BY CustomerID
) A ON C.CustomerID = A.CustomerID;
After CTE (Clearer Structure):
```

WITH CTE

```
WITH AvgOrdersPerCustomer AS (
    SELECT CustomerID, AVG(Total) AS AvgOrder
    FROM Orders
    GROUP BY CustomerID
)
SELECT C.City, A.AvgOrder
FROM Customers C
JOIN AvgOrdersPerCustomer A ON C.CustomerID = A.CustomerID;

```


2. Reusability within a Single Query

If you need to perform the same complex calculation or filtering step multiple times within a large final query, defining
it once as a CTE prevents redundant code and can sometimes lead to better performance by allowing the database to 
optimize the execution plan.

