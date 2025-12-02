# Customers Who Bought All Products

## Problem Statement

### Table: Customer

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| customer_id | int     |
| product_key | int     |
+-------------+---------+
```
- This table may contain duplicate rows.
- `customer_id` is not NULL.
- `product_key` is a foreign key (references the `Product` table).

### Table: Product

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| product_key | int     |
+-------------+---------+
```
- `product_key` is the primary key (column with unique values) for this table.

## Task

Write an SQL solution to find all customer IDs from the `Customer` table who have purchased all products available in the `Product` table.

Return the result table in any order.

## Example

### Input

**Customer table:**
```
+-------------+-------------+
| customer_id | product_key |
+-------------+-------------+
| 1           | 5           |
| 2           | 6           |
| 3           | 5           |
| 3           | 6           |
| 1           | 6           |
+-------------+-------------+
```

**Product table:**
```
+-------------+
| product_key |
+-------------+
| 5           |
| 6           |
+-------------+
```

### Output
```
+-------------+
| customer_id |
+-------------+
| 1           |
| 3           |
+-------------+
```

### Explanation
Customers with IDs 1 and 3 have purchased all available products (products 5 and 6).

## Solution
-- Table containing purchases (Customer ID and Product Key)
-- We only want groups (customers) where the number of distinct products they bought
-- is equal to the total number of distinct products in the Product table. 
-- Subquery 1: Calculate the total number of unique products available.

```sql
SELECT
    customer_id
FROM
    Customer 
GROUP BY
    customer_id
HAVING
    COUNT(DISTINCT product_key) = (
        SELECT 
            COUNT(DISTINCT product_key) 
        FROM 
            Product
    );
```
