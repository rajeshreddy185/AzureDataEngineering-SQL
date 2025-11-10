** Aggregate functions **

Aggregate functions perform a calculation on a set of rows and return a single summary value. They are typically used
with the GROUP BY clause to calculate summary metrics for distinct groups of data, but they can also be used without
GROUP BY to summarize the entire result set.

** Common Aggregate Functions **

##### COUNT(): Returns the number of rows in a group.

Purpose: Returns the number of rows in a specified group or the total number of rows.
Example: Count the total number of products in stock.

SQL

```
SELECT
    COUNT(ProductID) AS TotalProducts
FROM
    Products;
```

###### GROUP BY: Count products per category.

SQL
```
SELECT
    Category,
    COUNT(ProductID) AS ProductsPerCategory
FROM
    Products
GROUP BY
    Category;
```

##### SUM(): Returns the sum of a numeric column.

Purpose: Calculates the total sum of a numeric column.

Example: Calculate the total number of units currently in stock across all products.

SQL
```
SELECT
    SUM(UnitsInStock) AS TotalUnitsInStock
FROM
    Products;
```

Example with GROUP BY: Total units in stock per category.

SQL
```
SELECT
    Category,
    SUM(UnitsInStock) AS UnitsPerCategory
FROM
    Products
GROUP BY
    Category;
```

##### AVG(): Returns the average of a numeric column.

Purpose: Calculates the average (mean) value of a numeric column.
Example: Find the average price of all products.

SQL
```
SELECT
    AVG(Price) AS AverageProductPrice
FROM
    Products;
```

Example with GROUP BY: Average price per category.

SQL
```
SELECT
    Category,
    AVG(Price) AS AveragePrice
FROM
    Products
GROUP BY
    Category;
```

##### MIN(): Returns the minimum value in a column.

Purpose: Returns the smallest (minimum) value in a column. This works on numeric, date, and text data.
Example: Find the lowest price among all products.

SQL

```
SELECT
    MIN(Price) AS LowestPrice
FROM
    Products;
```
Example with GROUP BY: Lowest price per category.

SQL
```
SELECT
    Category,
    MIN(Price) AS LowestPrice
FROM
    Products
GROUP BY
    Category;
```

##### MAX(): Returns the maximum value in a column.

Purpose: Returns the largest (maximum) value in a column. This works on numeric, date, and text data.
Example: Find the highest price among all products.

SQL
```
SELECT
    MAX(Price) AS HighestPrice
FROM
    Products;
```


Using Aggregate Functions
If you use an aggregate function without a GROUP BY clause, it operates on and returns a summary value for all rows that meet the WHERE condition (if specified).

Example: Find the total sales amount from the entire Orders table.

SQL
```
SELECT
    SUM(OrderAmount) AS TotalGlobalSales
FROM
    Orders
WHERE
    OrderDate BETWEEN '2025-01-01' AND '2025-06-30';
```
Example with GROUP BY: Highest price per category.

SQL
```
SELECT
    Category,
    MAX(Price) AS HighestPrice
FROM
    Products
GROUP BY
    Category;
```

2. Aggregating with GROUP BY (The Common Use Case)
This is the most powerful use, where you summarize data within specific categories. The GROUP BY clause divides the result set into subsets, and the aggregate function then runs once for each subset.

Example: Find the total orders and maximum price for each category.

SQL
```
SELECT
    Category,
    COUNT(ProductID) AS TotalProducts, -- Counts products in each group (category)
    MAX(Price) AS MaxPriceInGroup      -- Finds the highest price in each group
FROM
    Products
GROUP BY
    Category; -- Groups all rows with the same Category value
```

3. Filtering Aggregated Results with HAVING
You cannot use the WHERE clause to filter the results of an aggregate function (e.g., WHERE COUNT(OrderID) > 100). 
Instead, you must use the HAVING clause, which filters groups after the aggregation has been performed.
Example: Find the average salary for each department, but only include departments that have more than 10 employees.

SQL

```
SELECT
    Department,
    AVG(Salary) AS AvgDepartmentSalary
FROM
    Employees
GROUP BY
    Department
HAVING
    COUNT(EmployeeID) > 10; -- Filters groups where the employee count exceeds 10
```










