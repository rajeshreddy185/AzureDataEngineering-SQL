** OFFSET **

The OFFSET clause is used to skip a specified number of rows before starting to return the result set. It is almost always used with ORDER BY to ensure consistent results.

Example: Get the second page of results, where each page has 10 records (i.e., skip the first 10 records).

SQL

```
SELECT
    ProductName,
    Price
FROM
    Products
ORDER BY
    ProductName ASC
LIMIT 10      -- Return 10 rows (the size of the page)
OFFSET 10;    -- Skip the first 10 rows (the first page)

```