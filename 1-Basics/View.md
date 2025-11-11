** VIEW **

A view is a virtual table whose content is defined by a stored SQL query. It does not physically store data itself; 
instead, it presents data retrieved from one or more underlying tables when it is queried.

##### Creation Command (CREATE VIEW)
The command defines the view by providing it a name and specifying the SELECT query that determines its content.

SQL
```
CREATE VIEW HighSalariedEmployees AS
SELECT
    EmployeeID,
    FirstName,
    Salary
FROM
    Employees
WHERE
    Salary > 70000;
```


**Key Characteristics**
| Feature | View |
|---------|------|
| Storage | Does not store data; it stores only the definition (the SELECT query). |
| Object Type | A virtual, logical object. |
| Data Integrity | Does not enforce constraints directly, as it relies on the underlying base tables for integrity. |
| Modification | INSERT, UPDATE, and DELETE are often restricted or complex, especially for views based on multiple tables or aggregate functions. |
| Definition | Defined by a SELECT query. |


