### Index

An Index in SQL is a special lookup structure associated with a table that speeds up the retrieval of rows by providing 
quick access to data based on key values. It works conceptually like an index in the back of a textbook: instead of 
reading every page (row) sequentially, you can look up a topic (value) in the index and go directly to the page
number (memory location) where the data resides.

Indices are critical for database performance, especially on large tables, but they come with a trade-off.

##### Speed Up Retrieval: 
        The primary benefit is dramatically reducing the time it takes for SELECT queries to return results, particularly
        those involving WHERE, JOIN, and ORDER BY clauses.

#####  Data Structure: 
        Most SQL indexes are implemented using highly efficient data structures like B-Trees  or variations, which allow 
        the database engine to quickly traverse the index to find the physical location of the requested data.

##### Trade-off: 
        While they speed up reading, indexes can slow down data modification (DML) operations (INSERT, UPDATE, DELETE). 
        This is because every time data is changed, the corresponding index must also be updated and maintained.


#### Types of Indexes

###### 1. Clustered Index

**Definition**: 
        A clustered index determines the physical order in which the data is stored in the table. Because the 
        data itself can only be sorted in one physical order, a table can have only one clustered index.

**Best Use**: 
        Typically created on the Primary Key column, as this is the most common way to look up specific rows.

**Effect**: 
        The actual data rows are stored in the leaf nodes of the index structure.

###### 2. Non-Clustered Index 

**Definition**: 
        A non-clustered index does not affect the physical order of the data. Instead, it stores the indexed 
        column values and pointers (row IDs or primary keys) that point to the actual physical location of the data in 
        the table. A table can have multiple non-clustered indexes.

**Best Use**: 
        Used on columns frequently used in WHERE clauses, JOIN conditions, or ORDER BY clauses, but are not the primary key.

**Effect**: 
        It's a separate structure from the data.


##### DDL Commands for Indexes

Indexes are created and managed using DDL (Data Definition Language) commands.

**1. CREATE INDEX**
Used to build a new index on an existing table.

SQL
```
-- Creating a non-clustered index on the LastName column
CREATE INDEX IX_Employees_LastName 
ON Employees (LastName);

```

**2. CREATE UNIQUE INDEX**
Ensures that all values in the indexed column(s) are unique (similar to a unique constraint, but with index benefits).

SQL
```
-- Ensuring unique email addresses
CREATE UNIQUE INDEX UIX_Customers_Email 
ON Customers (Email);

```

**3. DROP INDEX**
Used to remove an index from a database table.

SQL
```
-- Removing the index
DROP INDEX IX_Employees_LastName ON Employees;

```