** LIMIT **
These two clauses are often used together to implement pagination, allowing you to retrieve only a specific window or 
"page" of results, which is crucial for web applications.

The LIMIT clause is used to constrain the number of rows returned by the query. 
(Note: In SQL Server and Oracle, this functionality is often achieved using TOP or FETCH FIRST/NEXT).

Example: Get the 5 most recent orders.

SQL

```

SELECT *
FROM Orders
ORDER BY OrderDate DESC
LIMIT 5; -- Returns only the first 5 rows

```