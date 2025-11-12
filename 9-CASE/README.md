** CASE STATEMENTS **

The SQL keyword CASE is a powerful form of conditional logic that allows you to perform 
IF/THEN/ELSE logic directly within your SQL queries.

It is primarily used in SELECT statements to create new columns or modify the display of existing data based 
on a set of conditions. It can also be used in WHERE, ORDER BY, and GROUP BY clauses.

Two Main Forms of CASE
There are two primary ways to structure a CASE expression: the Simple CASE and the Searched CASE.

##### 1. Simple CASE Expression
This form compares a single input expression against a set of simple values.
Logic: Checks if input_expression is equal to value1, value2, etc.
Best For: Equality checks against a limited number of known values.

SQL
```
SELECT
    ProductName,
    Price,
    CASE Price
        WHEN 10 THEN 'Budget'      -- IF Price = 10 THEN 'Budget'
        WHEN 25 THEN 'Standard'    -- IF Price = 25 THEN 'Standard'
        ELSE 'Premium'             -- ELSE 'Premium'
    END AS PriceCategory
FROM
    Products;
```

##### 2. Searched CASE Expression (More Flexible)

This form allows you to specify a different, complex Boolean condition for each WHEN clause. 
It is the most common and flexible way to use CASE.
Logic: Evaluates a specific condition (condition1, condition2, etc.) which can include comparison 
operators like >, <, BETWEEN, etc.

Best For: Range checks, complex logic, and comparing multiple columns.

SQL
```
SELECT
    EmployeeName,
    Salary,
    CASE
        WHEN Salary > 80000 THEN 'Senior'     -- IF Salary > 80000
        WHEN Salary BETWEEN 50000 AND 80000 THEN 'Mid-Level'
        WHEN Salary < 50000 THEN 'Junior'     -- IF Salary < 50000
        ELSE 'Undefined'                      -- Handles any case not explicitly matched
    END AS EmployeeLevel
FROM
    Employees;
```


Key Rules for CASE
** END Clause is Mandatory: Every CASE statement must conclude with the END keyword.
** ELSE Clause is Optional: If the ELSE clause is omitted, and none of the WHEN conditions are met, the expression 
                            returns NULL. It's best practice to always include an ELSE clause for clarity and to 
                            handle unmatched values.
** Execution Order: The WHEN conditions are evaluated in order. The query stops at the first condition that evaluates 
                    to TRUE, and the corresponding THEN result is returned.

** It can also be used in WHERE, ORDER BY, and GROUP BY clauses **

The CASE expression can indeed be used in the WHERE, ORDER BY, and GROUP BY clauses in addition to the SELECT clause, 
making it a very versatile tool for conditional logic in SQL.

Here's a breakdown of how it's used in each of these contexts:

##### 1. ORDER BY Clause (Conditional Sorting)
Using CASE in ORDER BY allows you to define a custom sort order based on column values or other criteria.

Example: Sort Products by a specific Category first.
SQL
```
SELECT ProductName, Category
FROM Products
ORDER BY
    CASE
        WHEN Category = 'Electronics' THEN 1  -- Electronics come first (assigned sort value 1)
        WHEN Category = 'Clothing' THEN 2      -- Clothing comes second
        ELSE 3                                 -- Everything else comes last
    END,
    ProductName ASC; -- Secondary sort by name
```

###### 2. GROUP BY Clause (Conditional Grouping) 
Using CASE in GROUP BY allows you to group rows based on dynamic conditions or ranges that are not directly defined by 
a simple column value.

Example: Group Customers by large spending tiers.
SQL
```
SELECT
    CASE
        WHEN TotalSpent >= 1000 THEN 'High Value'
        WHEN TotalSpent BETWEEN 500 AND 999 THEN 'Mid Value'
        ELSE 'Low Value'
    END AS CustomerTier,
    COUNT(CustomerID) AS NumberOfCustomers
FROM Customers
GROUP BY
    CASE                                                    -- The grouping logic must exactly match the SELECT logic
        WHEN TotalSpent >= 1000 THEN 'High Value'
        WHEN TotalSpent BETWEEN 500 AND 999 THEN 'Mid Value'
        ELSE 'Low Value'
    END;
```

##### 3. WHERE Clause (Conditional Filtering) 

Using CASE in WHERE allows you to apply different filtering rules based on a condition that might involve a variable 
or another data point. While a standard WHERE clause is often simpler, CASE is useful when the 
condition itself needs to be dynamic.

Example: Filter based on whether a specific date exists.
SQL
``` 
    -- Retrieve all orders where either the 'PromotionCode' is present, 
    -- OR if no PromotionCode is required, then only retrieve orders placed after '2025-10-01'.
    SELECT *
    FROM Orders
    WHERE
        CASE
            WHEN PromotionCode IS NOT NULL THEN 1  -- Include if a promo code exists
            WHEN OrderDate > '2025-10-01' THEN 1   -- Include if order is recent
            ELSE 0                                 -- Exclude all others
        END = 1;                                   -- Only include rows where the CASE expression evaluated to 1 (True)
    
```   