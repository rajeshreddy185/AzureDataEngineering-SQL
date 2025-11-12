** JOINS **

JOINS are fundamental SQL operations used to combine columns and rows from two or more tables based on a related column 
between them. This related column is typically a Foreign Key (FK) in one table linking to a Primary Key (PK) in another.

The core purpose of a JOIN is to reconstruct a complete record from pieces of data stored across different, normalized tables.

Key Types of SQL JOINS
There are four primary types of JOINs, which determine how rows that do not have matching values in the other table are handled.

##### 1. INNER JOIN (or simply JOIN)

Purpose: Returns only the rows that have matching values in both tables. 
         Rows without a match in the other table are excluded from the result set.

Analogy: The intersection of two sets.

Example: Retrieve the name of a customer only if they have placed an order.

SQL
```
SELECT C.CustomerName, O.OrderID
FROM Customers C
INNER JOIN Orders O
ON C.CustomerID = O.CustomerID;
```

##### 2. LEFT JOIN (or LEFT OUTER JOIN)

Purpose: Returns all rows from the left table, and the matching rows from the right table. If there is no match in the right table, 
         the columns from the right table will contain NULL values.

Analogy: The left set plus the intersection.

Example: Retrieve the name of all customers, including those who have not placed an order (their OrderID column will be NULL).

SQL
```
SELECT C.CustomerName, O.OrderID
FROM Customers C
LEFT JOIN Orders O
ON C.CustomerID = O.CustomerID;

```

##### 3. RIGHT JOIN (or RIGHT OUTER JOIN)

Purpose: Returns all rows from the right table, and the matching rows from the left table. If there is no match in the 
         left table, the columns from the left table will contain NULL values.

Analogy: The right set plus the intersection.

Example: Retrieve all orders, including those where the CustomerID might be missing or invalid (the CustomerName will be NULL).

SQL
```
SELECT C.CustomerName, O.OrderID
FROM Customers C
RIGHT JOIN Orders O
ON C.CustomerID = O.CustomerID;

```

##### 4. FULL JOIN (or FULL OUTER JOIN)

Purpose: Returns all rows when there is a match in one of the tables. It combines the results of both LEFT JOIN and  
         RIGHT JOIN. Rows without a match in either table will have NULL values in the columns of the non-matching table.

Analogy: The union of both sets.

Example: Retrieve all customers and all orders, pairing them up where possible, and showing customers without orders 
         (OrderID is NULL) and orders without customers (CustomerName is NULL).

SQL

```
SELECT C.CustomerName, O.OrderID
FROM Customers C
FULL JOIN Orders O
ON C.CustomerID = O.CustomerID;

```
The ON Clause
The ON clause is essential for all JOIN types. It specifies the join condition—the logical rule that the database uses
to match rows between the tables.

Syntax: ```ON table1.column_a = table2.column_b```

Requirement: The data types of the columns being compared must be compatible.

##### SELF JOIN (Joining a Table to Itself)

A Self Join is not a distinct type but a concept where a table is joined to itself. This is typically used when a 
table has a foreign key that references its own primary key (e.g., an Employee table where an EmployeeID is linked to a ManagerID).

Mechanism: To perform a self-join, you must use table aliases to treat the single physical 
           table as if it were two separate tables.

SQL
```
SELECT 
    E.EmployeeName,
    M.EmployeeName AS ManagerName
FROM
    Employees E -- Alias E for the Employee record
INNER JOIN
    Employees M -- Alias M for the Manager record
ON E.ManagerID = M.EmployeeID; -- Match the employee's manager ID to the manager's employee ID


```