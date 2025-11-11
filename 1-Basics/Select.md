** SELECT **

The SELECT statement is the most frequently used and fundamental command in SQL, as it is used to retrieve data from one or more tables in a database. It is the only command that belongs to the DQL (Data Query Language) category.

The power of SELECT comes from its ability to be combined with various clauses to filter, sort, group, and combine the returned data.

Basic SELECT Syntax 🔎
The fundamental structure of a SELECT query is:

SQL

```
SELECT column1, column2, ...
FROM table_name;
```
SELECT: Specifies the columns you want to view.

FROM: Identifies the table(s) where the data resides.

Syntax Variant	Purpose	Example
| Syntax Variant | Purpose | Example |
|----------------|---------|---------|
| SELECT * | Selects all columns from the specified table. | ```SELECT * FROM Customers;``` |
| SELECT DISTINCT | Returns only unique (non-duplicate) values from the specified column(s). | ```SELECT DISTINCT City FROM Customers;``` |
Export to Sheets
