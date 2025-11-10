DELETE (Data Removal): Filters which specific rows will be removed from the table.

SQL

```
DELETE FROM Orders
WHERE OrderDate < '2025-01-01'; 
```

Only orders placed before Jan 1, 2025 are deleted.