** GROUP BY **

The GROUP BY clause in SQL is used in conjunction with aggregate functions (like COUNT, SUM, AVG, MIN, MAX) to group 
rows that have the same values in specified columns into a set of summary rows.
Instead of returning a result for every single row, GROUP BY returns one row for each unique combination of values in 
the grouped column(s), with the aggregate function providing a summary result for that entire group.

Usage in SQL Commands 
SELECT (Data Retrieval): Groups rows based on one or more columns and applies aggregate functions to each group.

SQL

Syntax and Structure 📐
The GROUP BY clause must appear after the FROM and WHERE clauses, but before the HAVING and ORDER BY clauses.

SQL

```
SELECT
    column1, -- The column(s) being grouped
    aggregate_function(column2) -- The calculated summary value
FROM
    table_name
WHERE
    condition -- (Optional) Filters individual rows before grouping
GROUP BY
    column1, column3, ... -- Lists all non-aggregated columns from the SELECT list
HAVING
    aggregate_condition -- (Optional) Filters groups after grouping and aggregation
ORDER BY
    column_or_aggregate;
```

 **Key Rules and Requirements 💡**

Select List Constraint: Any column listed in the SELECT statement that is not an aggregate function (e.g., SUM(Sales)) 
 must be listed in the GROUP BY clause. This ensures that the grouping is explicitly defined for every non-aggregated 
 value you want to see. 

Order of Execution: The database processes the query in this conceptual 
order: FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY.

**Total Sales per Customer**
To find the total amount spent by each customer, you must group the sales data by CustomerID.

SQL

```
SELECT
    CustomerID,
    SUM(OrderAmount) AS TotalSpent -- Aggregate function
FROM
    Orders
GROUP BY
    CustomerID;
```

**Counting Records in Multiple Groups**
To find the number of employees in each department and city, you group by both columns.

SQL

```
SELECT
    Department,
    City,
    COUNT(*) AS EmployeeCount -- Aggregate function
FROM
    Employees
GROUP BY
    Department, City;
```


**Filtering Groups with HAVING**
NOTE: The WHERE clause cannot filter on the results of an aggregate function. To filter the aggregated groups, you use HAVING.

SQL

```
SELECT
    Region,
    AVG(Sales) AS AverageSales
FROM
    SalesData
GROUP BY
    Region
HAVING
    AVG(Sales) > 50000;
```