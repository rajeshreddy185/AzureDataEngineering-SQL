# Product Price at a Given Date

## Problem Statement

### Table: Products

| Column Name | Type | Description |
|-------------|------|-------------|
| product_id  | int  | Product identifier |
| new_price   | int  | New price of the product |
| change_date | date | Date when the price was changed |

- `(product_id, change_date)` is the primary key (combination of columns with unique values)
- Each row indicates that the price of some product was changed to a new price at some date
- All products initially have a price of 10

## Task

Find the prices of all products on the date `2019-08-16`.

### Requirements:
- Return the result table in any order
- If there is no price change for a product before or on the given date, use the initial price of 10

## Example

### Input

**Products table:**
| product_id | new_price | change_date |
|------------|-----------|-------------|
| 1          | 20        | 2019-08-14  |
| 2          | 50        | 2019-08-14  |
| 1          | 30        | 2019-08-15  |
| 1          | 35        | 2019-08-16  |
| 2          | 65        | 2019-08-17  |
| 3          | 20        | 2019-08-18  |

### Output
| product_id | price |
|------------|-------|
| 1          | 35    |
| 2          | 50    |
| 3          | 10    |

## Solution

```sql
WITH LatestPriceBeforeDate AS (
    SELECT 
        product_id,
        new_price,
        ROW_NUMBER() OVER (
            PARTITION BY product_id 
            ORDER BY change_date DESC
        ) as rn
    FROM 
        Products
    WHERE 
        change_date <= '2019-08-16'
),
DistinctProducts AS (
    SELECT DISTINCT product_id
    FROM Products
)
SELECT 
    dp.product_id,
    COALESCE(lp.new_price, 10) as price
FROM 
    DistinctProducts dp
LEFT JOIN 
    LatestPriceBeforeDate lp 
    ON dp.product_id = lp.product_id 
    AND lp.rn = 1
ORDER BY 
    dp.product_id;

```


```sql

SELECT 
    p1.product_id,
    COALESCE(
        (SELECT new_price 
         FROM Products p2 
         WHERE p2.product_id = p1.product_id 
           AND p2.change_date <= '2019-08-16'
         ORDER BY p2.change_date DESC 
         LIMIT 1),
        10
    ) as price
FROM 
    (SELECT DISTINCT product_id FROM Products) p1
ORDER BY 
    p1.product_id;
    
```