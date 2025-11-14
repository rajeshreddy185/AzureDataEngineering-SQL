** Type Casting **

SQL function CAST are used for explicitly changing the data type of an expression, column, or value. 
This is known as type casting or type conversion.

1. CAST() 

Purpose: Converts an expression from one data type to another.

Syntax: Clean, simple, and universal.

SQL

CAST ( expression AS new_data_type )
Example
Converting a numeric Price column to a VARCHAR so it can be concatenated with a currency symbol:

SQL
```
SELECT
    ProductName,
    'Price: $' + CAST(Price AS VARCHAR(10)) AS FormattedPrice
FROM
    Products;
```

