**  SET  **
The SQL SET command is a versatile statement used to change a runtime parameter, control session behavior, or define 
transaction characteristics. It doesn't modify data within tables (like DML) or the table structure (like DDL), 
but rather affects the environment or rules under which SQL statements execute.


The specific functions of the SET command vary significantly between database systems (SQL Server, PostgreSQL, MySQL, 
etc.), but it primarily falls under the TCL (Transaction Control Language) or general session control.

Common Uses of the SET Command 
1. Controlling Transaction Behavior (TCL)
This is one of the most common and important uses, defining how the current transaction operates in terms of isolation and access.

Use Case          Example (Standard SQL/PostgreSQL/DB2)             Effect
------------------------------------------------------------------------------------------------------------------ --------------------------------------------------------------------------------------------------------------------
Isolation Level    SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;    Controls how the current transaction interacts with concurrent transactions to prevent data anomalies.
Access Mode        SET TRANSACTION READ ONLY;                       Prevents the current transaction from making any changes (INSERT, UPDATE, DELETE) to the database.
Transaction Name   SET TRANSACTION NAME 'Update_Order_Status';      Assigns a name to the current transaction for easier identification in logs (vendor-specific).


2. Managing Session Environment
These commands affect how the user's current session processes data and communicates with the database.

Use Case	    Example (SQL Server)	               Example (PostgreSQL)
Row Count  	    SET NOCOUNT ON;     	                Prevents the message indicating the number of rows affected by a query (e.g., "3 rows affected").
Time Zone	    SET TIME ZONE 'America/Los_Angeles';	Changes the time zone used for interpreting and displaying date/time values in the current session.
Date Format	    SET DATEFORMAT 'DMY';	                Defines the expected order of date components (Day/Month/Year).


3. Setting User-Defined Variables
In some SQL environments, SET is used to define a local variable for use within a script or session.

Example (MySQL/MariaDB):

SQL
```
SET @LastOrderID = 1050;
SELECT * FROM Orders WHERE OrderID = @LastOrderID;
```

Example (SQL Server):

SQL
```
DECLARE @MinPrice DECIMAL(10, 2);
SET @MinPrice = 10.00;
SELECT * FROM Products WHERE Price > @MinPrice;
```