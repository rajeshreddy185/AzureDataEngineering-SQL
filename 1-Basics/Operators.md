Operators Used in the WHERE Clause
The WHERE clause relies on operators to define conditions.

1. Comparison Operators
These compare a column value to another value.

Operator | Description | Example
---------|-------------|---------
=        | Equal to    | Salary = 50000
!= or <> | Not equal to| City <> 'Paris'
>        | Greater than| Quantity > 10
<        | Less than   | Age < 30
>=       | Greater than or equal to| OrderTotal >= 100
<=       | Less than or equal to   | StockLevel <= 5

Export to Sheets
2. Logical Operators
These combine multiple conditions.

Operator | Description | Example
---------|-------------|---------
AND       | True if both conditions are true. | Country = 'USA' AND City = 'New York'
OR        | True if at least one condition is true. | City = 'Paris' OR City = 'London'
NOT       | Negates the condition. | NOT (Country = 'USA')

Export to Sheets
3. Special Operators
These perform specific pattern or set matching.

Operator | Description | Example
---------|-------------|---------
LIKE	| Used for pattern matching (with wildcards like `%` and `_`).	| ProductName LIKE 'A%' (starts with 'A')
IN	| Checks if a value matches any value in a list.	| Status IN ('New', 'Pending', 'Processing')
BETWEEN	| Checks if a value is within a range (inclusive).	| HireDate BETWEEN '2024-01-01' AND '2024-12-31'
IS NULL	| Checks for a NULL (missing or undefined) value.	| Email IS NULL
IS NOT NULL	| Checks for a non-NULL value.	| Email IS NOT NULL
