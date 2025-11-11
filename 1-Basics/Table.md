** Table **

A table is a physical object that stores data directly in the database.


Creation Command (CREATE TABLE)

The command explicitly defines the structure, including all columns, data types, and constraints.

SQL
```
CREATE TABLE Employees (
    EmployeeID INT PRIMARY KEY,
    FirstName VARCHAR(50) NOT NULL,
    Salary DECIMAL(10, 2),
    HireDate DATE
);
```

**Key Characteristics**

| Feature | Table |
|---------|-------|
| Storage | Stores data physically on the disk. |
| Object Type | A primary database object. |
| Data Integrity | Enforces constraints (PRIMARY KEY, NOT NULL, CHECK, FOREIGN KEY) to guarantee data integrity. |
| Modification | Supports all DML commands: INSERT, UPDATE, and DELETE. |
| Definition | Defined by columns and data types. |

