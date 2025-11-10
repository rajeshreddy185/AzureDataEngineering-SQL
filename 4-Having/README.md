**SQL HAVING**

The SQL HAVING clause is used to filter the results of a GROUP BY clause based on a specified condition. 
It is essential because the standard WHERE clause cannot directly reference or filter on the results of an 
aggregate function (like SUM(), COUNT(), or AVG()).

In essence, HAVING filters the groups, whereas WHERE filters the individual rows before any grouping or aggregation occurs.

How HAVING Works 📐
Grouping: The database first groups the rows using the GROUP BY clause and calculates the aggregate values for each group.

Filtering: The HAVING clause then evaluates a condition against those calculated aggregate values. Only the groups that
meet the HAVING condition are included in the final result set.

**Syntax and Order of Execution**
The HAVING clause must always follow the GROUP BY clause.

SQL
```
SELECT
    column1,
    aggregate_function(column2)
FROM
    table_name
WHERE
    individual_row_condition -- Applied first
GROUP BY
    column1
HAVING
    aggregate_condition; -- Applied second, to the groups
```

GROUP BY: The query first groups the data by CustomerID and counts the orders for each.
HAVING: It then filters the resulting groups to keep only those where the count is greater than 5.

SQL
```
SELECT
    CustomerID,
    COUNT(OrderID) AS TotalOrders
FROM
    Orders
GROUP BY
    CustomerID
HAVING
    COUNT(OrderID) > 5; -- Only groups with more than 5 orders are returned

```

**Contrast with WHERE**

WHERE: Filters rows based on a column's original value.
Example: WHERE OrderDate > '2025-10-01' (Filters individual order rows).

HAVING: Filters groups based on an aggregated value.
Example: HAVING SUM(OrderAmount) > 5000 (Filters groups based on total sales).