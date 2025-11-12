** SET OPERATIONS **

SQL set operators used to combine the results of two or more independent SELECT queries into a single result set. 
For these operations to work correctly, all participating SELECT statements must have:

1. The same number of columns.
2. The columns must have compatible data types (though the COLUMN names don't have to match).
3. The columns must be in the same order.

The set operators are:

1. UNION
2. UNION ALL
3. INTERSECT
4. EXCEPT



##### UNION

The UNION operator combines the result sets of two or more SELECT statements and removes duplicate rows.

SQL
```
SELECT column1, column2 FROM TableA
UNION
SELECT column1, column2 FROM TableB;

```

##### UNION ALL

The UNION ALL operator combines the result sets of two or more SELECT statements and includes all rows, 
including duplicates.

SQL
```
SELECT column1, column2 FROM TableA
UNION ALL
SELECT column1, column2 FROM TableB;
```


##### INTERSECT  (Finding Common Rows)
The INTERSECT operator returns only the rows that are common to both SELECT statements. 
In other words, a row must exist in the result set of the first query AND the second query to be included.

SQL
```
SELECT column1, column2 FROM TableA
INTERSECT
SELECT column1, column2 FROM TableB;
```


##### EXCEPT (Finding Unique Rows)

The EXCEPT operator returns all rows from the first SELECT statement that are not present in the second SELECT statement. 

SQL

```
SELECT column1, column2 FROM TableA
EXCEPT
SELECT column1, column2 FROM TableB;
```

