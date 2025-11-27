# Rearrange Products Table

## Problem Description

### Table: Products

| Column Name | Type | Description |
|-------------|------|-------------|
| product_id  | int  | Primary key |
| store1      | int  | Price in store1 (null if not available) |
| store2      | int  | Price in store2 (null if not available) |
| store3      | int  | Price in store3 (null if not available) |

## Task

Convert the table from wide format to long format where:
- Each row represents a product's price in a specific store
- Exclude any rows where the price is null
- Result should have columns: `product_id`, `store`, `price`
- Order doesn't matter

## Example

### Input:

**Products table:**
| product_id | store1 | store2 | store3 |
|------------|--------|--------|--------|
| 0          | 95     | 100    | 105    |
| 1          | 70     | null   | 80     |

### Output:
| product_id | store  | price |
|------------|--------|-------|
| 0          | store1 | 95    |
| 0          | store2 | 100   |
| 0          | store3 | 105   |
| 1          | store1 | 70    |
| 1          | store3 | 80    |

### Explanation:
- Product 0 is available in all three stores with prices 95, 100, and 105
- Product 1 is not available in store2 (null), so it's excluded from results
- Each row shows one product's price in one store

## Solution:

### Using UNION ALL (Works in all SQL databases)
```sql
SELECT 
    product_id,
    'store1' AS store,
    store1 AS price
FROM 
    Products
WHERE 
    store1 IS NOT NULL

UNION ALL

SELECT 
    product_id,
    'store2' AS store,
    store2 AS price
FROM 
    Products
WHERE 
    store2 IS NOT NULL

UNION ALL

SELECT 
    product_id,
    'store3' AS store,
    store3 AS price
FROM 
    Products
WHERE 
    store3 IS NOT NULL
ORDER BY 
    product_id, store;