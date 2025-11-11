** TRUNCATE TABLE **
The TRUNCATE TABLE command is a DDL (Data Definition Language) statement used to quickly delete all rows from a table.

Purpose: To rapidly remove all data from a table while keeping the table structure intact.

Key Characteristics:
```
Feature	    TRUNCATE TABLE	                                        DELETE FROM table
Speed	    Faster; it deallocates the data pages and logs          Slower; it logs the deletion of every single row.
            only the deallocation.	
DDL/DML	    DDL (Data Definition Language - meaning it changes      DML (Data Manipulation Language - fully transactional 
            the table structure metadata, often making it           and rollback-able).
            less transactional/rollback-able).	
WHERE   	Not allowed; it clears the entire table.	            Allowed; can delete specific rows.
Identity 	Usually resets Identity/Auto-increment counters         Does not reset Identity counters.
            back to the seed value.	

```
Syntax:

SQL
```
TRUNCATE TABLE TempLogData;
```
-- Clears all data from TempLogData instantly, but the table structure remains.