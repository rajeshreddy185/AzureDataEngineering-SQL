** DISTINCT ️** (Removing Duplicates)
The DISTINCT keyword is used immediately after the SELECT keyword to eliminate duplicate rows from the result set, 
returning only the unique values.
Scope: DISTINCT applies to all columns specified in the SELECT list. A row is considered a duplicate only if the values
in all selected columns are identical.

Example: List all the unique cities from which customers hail.

SQL

```
SELECT DISTINCT
    City
FROM
    Customers;
```


Example (Multiple Columns): Find unique combinations of City and Country.

SQL
```
SELECT DISTINCT
    City,
    Country
FROM
    Customers; -- Only returns rows where the combined (City, Country) value is unique

```