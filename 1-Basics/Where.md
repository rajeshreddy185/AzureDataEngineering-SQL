** SQL WHERE **

The SQL WHERE clause is a fundamental component of the SELECT, UPDATE, and DELETE statements. Its primary function is to 
filter rows based on one or more specified conditions.

The WHERE clause dictates which rows from the table(s) will be included in the final result set for a SELECT query, 
or which rows will be modified by an UPDATE or DELETE command.

Usage in SQL Commands 
SELECT (Data Retrieval): Filters which rows are returned to the user.

SQL

`SELECT CustomerName, City
FROM Customers
WHERE City = 'London'; `-- Only rows where the City column is 'London' are returned.