# Product Sales Analysis III

## Problem Statement

### Table: Sales

```
+-------------+-------+
| Column Name | Type  |
+-------------+-------+
| sale_id     | int   |
| product_id  | int   |
| year        | int   |
| quantity    | int   |
| price       | int   |
+-------------+-------+
```
- `(sale_id, year)` is the primary key (combination of columns with unique values).
- Each row records a sale of a product in a given year.
- A product may have multiple sales entries in the same year.
- `price` is the per-unit price.

## Task

Write a solution to find all sales that occurred in the first year each product was sold.

### Requirements:
1. For each `product_id`, identify the earliest `year` it appears in the `Sales` table.
2. Return all sales entries for each product in their respective first year.
3. Return a table with columns: `product_id`, `first_year`, `quantity`, and `price`.
4. The result can be in any order.

## Example

### Input

**Sales table:**
```
+---------+------------+------+----------+-------+
| sale_id | product_id | year | quantity | price |
+---------+------------+------+----------+-------+ 
| 1       | 100        | 2008 | 10       | 5000  |
| 2       | 100        | 2009 | 12       | 5000  |
| 7       | 200        | 2011 | 15       | 9000  |
+---------+------------+------+----------+-------+
```

### Output
```
+------------+------------+----------+-------+
| product_id | first_year | quantity | price |
+------------+------------+----------+-------+
| 100        | 2008       | 10       | 5000  |
| 200        | 2011       | 15       | 9000  |
+------------+------------+----------+-------+
```

## Solution

```sql
WITH FirstYearSales AS (
    SELECT 
        product_id,
        MIN(year) AS first_year
    FROM 
        Sales
    GROUP BY 
        product_id
)
SELECT 
    s.product_id,
    fys.first_year,
    s.quantity,
    s.price
FROM 
    Sales s
JOIN 
    FirstYearSales fys ON s.product_id = fys.product_id AND s.year = fys.first_year;
```

### Explanation:
1. The CTE `FirstYearSales` finds the first year each product was sold by grouping by `product_id` and taking the `MIN(year)`.
2. We then join this back to the original `Sales` table to get all sales records that occurred in each product's first year.
3. The result includes the product ID, the first year it was sold, and the corresponding quantity and price from that year.

### Alternative Solution (without CTE):

```sql
SELECT 
    s1.product_id,
    s1.year AS first_year,
    s1.quantity,
    s1.price
FROM 
    Sales s1
WHERE 
    (s1.product_id, s1.year) IN (
        SELECT 
            product_id, 
            MIN(year) 
        FROM 
            Sales 
        GROUP BY 
            product_id
    );
```

Both solutions will produce the same result, but the first one using a CTE is generally more readable and maintainable.