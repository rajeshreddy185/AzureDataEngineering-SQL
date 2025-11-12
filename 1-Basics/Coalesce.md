** COALESCE **

##### 1. COALESCE() (Handling Missing Values)
The COALESCE function is used to return the first non-null expression in a list of expressions. 
It is a powerful way to substitute a meaningful default value when a column contains NULL.

Syntax:

SQL
```
COALESCE(expression1, expression2, expression3, ...)
```

Purpose: To provide a fallback value if the primary data source is missing.

How it Works: The database evaluates the expressions from left to right and stops as soon as it finds an expression 
              that is not NULL.


Example
Suppose an employee might have an Email1, but if that's missing (NULL), you want to use their Email2, and if that is 
also missing, you want to display the message "Contact HR".

SQL
```
SELECT
    EmployeeName,
    COALESCE(Email1, Email2, 'Contact HR') AS PreferredContact
FROM
    Employees;

```