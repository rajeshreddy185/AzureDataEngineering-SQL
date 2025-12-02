# Find Product Recommendation Pairs

## Problem Statement

### Table: ProductPurchases

| Column Name | Type | Description |
|-------------|------|-------------|
| user_id     | int  | User identifier |
| product_id  | int  | Product identifier |
| quantity    | int  | Quantity purchased |

### Table: ProductInfo

| Column Name | Type    | Description |
|-------------|---------|-------------|
| product_id  | int     | Product identifier |
| category    | varchar | Product category |
| price       | decimal | Product price |

### Key Points:
- `(user_id, product_id)` is the primary key for `ProductPurchases`
- `product_id` is the primary key for `ProductInfo`
- Need to find pairs of products frequently bought together by the same customers
- Only include pairs with at least 3 distinct customers

## Task

Find all distinct product pairs (where product1_id < product2_id) that:
1. Were purchased together by at least 3 different customers
2. Include category information for both products
3. Order results by customer count (descending), then product1_id (ascending), then product2_id (ascending)

## Example

### Input

**ProductPurchases table:**
| user_id | product_id | quantity |
|---------|------------|----------|
| 1       | 101        | 2        |
| 1       | 102        | 1        |
| 1       | 103        | 3        |
| 2       | 101        | 1        |
| 2       | 102        | 5        |
| 2       | 104        | 1        |
| 3       | 101        | 2        |
| 3       | 103        | 1        |
| 3       | 105        | 4        |
| 4       | 101        | 1        |
| 4       | 102        | 1        |
| 4       | 103        | 2        |
| 4       | 104        | 3        |
| 5       | 102        | 2        |
| 5       | 104        | 1        |

**ProductInfo table:**
| product_id | category    | price |
|------------|-------------|-------|
| 101        | Electronics | 100   |
| 102        | Books       | 20    |
| 103        | Clothing    | 35    |
| 104        | Kitchen     | 50    |
| 105        | Sports      | 75    |

### Output
| product1_id | product2_id | product1_category | product2_category | customer_count |
|-------------|-------------|-------------------|-------------------|----------------|
| 101         | 102         | Electronics       | Books             | 3              |
| 101         | 103         | Electronics       | Clothing          | 3              |
| 102         | 104         | Books             | Kitchen           | 3              |

## Solution
Get distinct products per user
Create all possible product pairs per user where product1 < product2
Count distinct users per product pair
Join with ProductInfo to get categories

```sql
WITH UserProducts AS (
    SELECT DISTINCT
        user_id,
        product_id
    FROM 
        ProductPurchases
),
ProductPairs AS (
    SELECT
        a.user_id,
        a.product_id AS product1_id,
        b.product_id AS product2_id
    FROM 
        UserProducts a
    JOIN 
        UserProducts b ON a.user_id = b.user_id
        AND a.product_id < b.product_id
),
PairCounts AS (
    SELECT
        product1_id,
        product2_id,
        COUNT(DISTINCT user_id) AS customer_count
    FROM 
        ProductPairs
    GROUP BY 
        product1_id, product2_id
    HAVING 
        COUNT(DISTINCT user_id) >= 3
)

SELECT
    pc.product1_id,
    pc.product2_id,
    p1.category AS product1_category,
    p2.category AS product2_category,
    pc.customer_count
FROM 
    PairCounts pc
JOIN 
    ProductInfo p1 ON pc.product1_id = p1.product_id
JOIN 
    ProductInfo p2 ON pc.product2_id = p2.product_id
ORDER BY 
    pc.customer_count DESC,
    pc.product1_id ASC,
    pc.product2_id ASC;