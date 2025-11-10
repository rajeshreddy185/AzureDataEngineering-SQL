# AzureDataEngineering-SQL

** SQL (Structured Query Language) **
SQL is the standard language for managing and manipulating relational databases. 
It's essential for anyone working with data.

** Relational Database **
A relational database organizes data into tables. 
The power of a relational database comes from its ability to define relationships between these tables using keys.

**Table**: A collection of related data organized in a grid format.

**Column** (or Field): Represents a single type of data (e.g., CustomerName, OrderID, Price).

**Row** (or Record/Tuple): Represents a single, complete entry in the table (e.g., one specific customer or one specific order).

**Primary Key** (PK): A column (or set of columns) that uniquely identifies each row in a table. It cannot contain NULL values.

**Foreign Key** (FK): A column in one table that links to the primary key in another table. This establishes the relationship.


**DQL: Data Query Language**

The most fundamental command in SQL is SELECT, used to retrieve data from a database.

The basic syntax for retrieving data is:

SQL

`SELECT column1, column2
FROM table_name;`


**DML: Data Manipulation Language**

These commands are used to modify the data stored in the tables.
like UPDATE, DELETE, INSERT
Updating Existing Data (UPDATE)
To modify data in existing rows:

SQL
`UPDATE table_name SET column1 = new_value1, column2 = new_value2,
WHERE condition;`
-- MUST include a WHERE clause to avoid updating ALL rows!

**Data Definition Language.**

It is a subset of SQL (Structured Query Language) used to define, modify, and manage the structure and schema of 
database objects like tables, indexes, views, sequences, and users.

Unlike DML (Data Manipulation Language), which affects the data within the tables, 
DDL affects the container or structure of the data itself.

Command	Example	Effect
CREATE TABLE	`CREATE TABLE Employees ( EmployeeID INT, LastName VARCHAR(255) );`	Creates a new table named Employees with two columns.
ALTER TABLE	`ALTER TABLE Employees ADD FirstName VARCHAR(255);`	Adds a new column named FirstName to the existing Employees table.
DROP TABLE	`DROP TABLE Employees;`	Completely removes the Employees table and all its data from the database.
TRUNCATE TABLE	`TRUNCATE TABLE Employees;`	Removes all rows from the table, but the table structure remains. It's faster than DELETE because it's a non-logged, DDL operation.
RENAME	`RENAME TABLE OldName TO NewName;`	Changes the name of an existing table or object.


**Data Control Language (DCL)**

DCL commands are used to manage user access and permissions (or privileges) to the database objects. This is crucial for 
database security, ensuring only authorized users can view or modify specific data.

Command	Purpose	Example Use
GRANT	Gives a user or role specific privileges on a database object.	`GRANT SELECT ON Customers TO 'Analyst_User';`
REVOKE	Removes (takes back) privileges that were previously granted.  `REVOKE DELETE ON Orders FROM 'Analyst_User';`


**Transaction Control Language (TCL)**

TCL commands are used to manage and control the transactions (a sequence of one or more SQL statements executed as 
a single unit) within the database. These commands are vital for maintaining data integrity and consistency, 
especially when multiple users are making changes concurrently.

Command	Purpose	Example Use
COMMIT	Permanently saves (makes final) the changes made during the current transaction.	`COMMIT`;
ROLLBACK	Undoes (reverts) all changes made in the current transaction since the last COMMIT or ROLLBACK.	`ROLLBACK`;
SAVEPOINT	Creates a named point within a transaction to which you can later roll back.	`SAVEPOINT A`;
SET TRANSACTION	Used to establish characteristics for the current transaction (e.g., isolation level).	`SET TRANSACTION READ WRITE`;