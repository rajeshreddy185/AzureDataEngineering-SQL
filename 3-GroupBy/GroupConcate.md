** GROUP_CONCAT **

The GROUP_CONCAT() function is a non-standard, but widely supported, aggregate function in SQL (most notably in MySQL and MariaDB) 
that is used to concatenate (join) strings from multiple rows into a single string.

It is an aggregate function because it operates on a set of grouped rows and returns one summary string for each group.

The primary use of GROUP_CONCAT() is to list all related items from a child table within a single row of the parent table, 
usually after a GROUP BY clause.


```
SELECT
    grouping_column,
    GROUP_CONCAT(column_to_concatenate
        [ORDER BY sort_column ASC|DESC]
        [SEPARATOR 'delimiter']
    ) AS concatenated_list
FROM
    table_name
GROUP BY
    grouping_column;
```


Clause	   | Purpose	                                                                            | Default Value
ORDER BY   | Specifies the order in which the individual strings are concatenated within the group.	| No specific order (unpredictable).
SEPARATOR  | Specifies the character(s) to place between the concatenated strings.	                | Comma (,).
DISTINCT   | (Optional) Allows you to concatenate only the unique values.                           | N/A


Real-World Example
Imagine you have two tables: Customers and Orders. You want to list every customer and see all the product names they have purchased in a single cell.

Query:

SQL
```

SELECT
    C.CustomerName,
    GROUP_CONCAT(P.ProductName
        ORDER BY P.ProductName ASC
        SEPARATOR '; '
    ) AS PurchasedProducts
FROM
    Customers C
JOIN
    Orders O ON C.CustomerID = O.CustomerID
JOIN
    Products P ON O.ProductID = P.ProductID
GROUP BY
    C.CustomerName;

```

Result:

```

CustomerName	PurchasedProducts
Alice Smith	    Laptop; Monitor; Mouse
Bob Jones	    Keyboard; Mouse; USB Drive
Charlie Brown	Charger; Monitor

```
Export to Sheets
