# Find Stores with Inventory Imbalance

## Problem Statement

### Table: stores

| Column Name | Type    | Description |
|-------------|---------|-------------|
| store_id    | int     | Unique identifier for each store |
| store_name  | varchar | Name of the store |
| location    | varchar | Location of the store |

### Table: inventory

| Column Name  | Type    | Description |
|--------------|---------|-------------|
| inventory_id | int     | Unique identifier for each inventory record |
| store_id     | int     | References stores.store_id |
| product_name | varchar | Name of the product |
| quantity     | int     | Quantity in stock |
| price        | decimal | Price of the product |

### Key Points:
- A store has inventory imbalance if the most expensive product's quantity is less than the cheapest product's quantity
- Imbalance ratio = (cheapest_quantity / most_expensive_quantity)
- Only include stores with at least 3 different products
- Round imbalance ratio to 2 decimal places
- Order by imbalance ratio (descending) and then by store name (ascending)

## Example

### Input

**stores table:**
| store_id | store_name    | location    |
|----------|---------------|-------------|
| 1        | Downtown Tech | New York    |
| 2        | Suburb Mall   | Chicago     |
| 3        | City Center   | Los Angeles |
| 4        | Corner Shop   | Miami       |
| 5        | Plaza Store   | Seattle     |

**inventory table:**
| inventory_id | store_id | product_name | quantity | price  |
|--------------|----------|--------------|----------|--------|
| 1            | 1        | Laptop       | 5        | 999.99 |
| 2            | 1        | Mouse        | 50       | 19.99  |
| 3            | 1        | Keyboard     | 25       | 79.99  |
| 4            | 1        | Monitor      | 15       | 299.99 |
| 5            | 2        | Phone        | 3        | 699.99 |
| 6            | 2        | Charger      | 100      | 25.99  |
| 7            | 2        | Case         | 75       | 15.99  |
| 8            | 2        | Headphones   | 20       | 149.99 |
| 9            | 3        | Tablet       | 2        | 499.99 |
| 10           | 3        | Stylus       | 80       | 29.99  |
| 11           | 3        | Cover        | 60       | 39.99  |
| 12           | 4        | Watch        | 10       | 299.99 |
| 13           | 4        | Band         | 25       | 49.99  |
| 14           | 5        | Camera       | 8        | 599.99 |
| 15           | 5        | Lens         | 12       | 199.99 |

### Output
| store_id | store_name   | location    | most_exp_product | cheapest_product | imbalance_ratio |
|----------|--------------|-------------|------------------|------------------|-----------------|
| 3        | City Center  | Los Angeles | Tablet           | Stylus           | 40.00           |
| 1        | Downtown Tech| New York    | Laptop           | Mouse            | 10.00           |
| 2        | Suburb Mall  | Chicago     | Phone            | Case             | 25.00           |

## Solution
-- Count products per store and filter for stores with >= 3 products
-- Find the most expensive product for each store
-- Find the cheapest product for each store
-- Combine results and calculate imbalance

```sql
WITH store_product_counts AS (
    
    SELECT 
        store_id
    FROM 
        inventory
    GROUP BY 
        store_id
    HAVING 
        COUNT(DISTINCT product_name) >= 3
),
most_expensive_products AS (
   
    SELECT 
        i.store_id,
        i.product_name AS most_exp_product,
        i.quantity AS most_exp_quantity,
        i.price AS most_exp_price
    FROM 
        inventory i
    INNER JOIN (
        SELECT 
            store_id, 
            MAX(price) AS max_price
        FROM 
            inventory
        GROUP BY 
            store_id
    ) max_prices ON i.store_id = max_prices.store_id AND i.price = max_prices.max_price
),
cheapest_products AS (
    
    SELECT 
        i.store_id,
        i.product_name AS cheapest_product,
        i.quantity AS cheapest_quantity,
        i.price AS cheapest_price
    FROM 
        inventory i
    INNER JOIN (
        SELECT 
            store_id, 
            MIN(price) AS min_price
        FROM 
            inventory
        GROUP BY 
            store_id
    ) min_prices ON i.store_id = min_prices.store_id AND i.price = min_prices.min_price
)

SELECT 
    s.store_id,
    s.store_name,
    s.location,
    mep.most_exp_product,
    cp.cheapest_product,
    ROUND(cp.cheapest_quantity / mep.most_exp_quantity, 2) AS imbalance_ratio
FROM 
    stores s
JOIN 
    store_product_counts spc ON s.store_id = spc.store_id
JOIN 
    most_expensive_products mep ON s.store_id = mep.store_id
JOIN 
    cheapest_products cp ON s.store_id = cp.store_id
WHERE 
    mep.most_exp_quantity < cp.cheapest_quantity
ORDER BY 
    imbalance_ratio DESC,
    s.store_name ASC;
```

```sql

SELECT
    S.store_id,
    S.store_name,
    S.location,
    
    E.product_name AS most_exp_product,
    
    C.product_name AS cheapest_product,
    
    ROUND(C.quantity * 1.0 / E.quantity, 2) AS imbalance_ratio
FROM
    Stores AS S
INNER JOIN
    (
        SELECT 
            store_id
        FROM Inventory
        GROUP BY store_id
        HAVING COUNT(DISTINCT product_name) >= 3
    ) AS PC 
    ON S.store_id = PC.store_id

INNER JOIN
    (
        SELECT 
            I1.store_id,
            I1.product_name,
            I1.quantity
        FROM Inventory AS I1
        INNER JOIN (
            SELECT 
                store_id, 
                MAX(price) AS max_price
            FROM Inventory
            GROUP BY store_id
        ) AS MaxPrice 
        ON I1.store_id = MaxPrice.store_id AND I1.price = MaxPrice.max_price
    ) AS E 
    ON S.store_id = E.store_id

INNER JOIN
    (
        SELECT 
            I2.store_id,
            I2.product_name,
            I2.quantity
        FROM Inventory AS I2
        INNER JOIN (
            SELECT 
                store_id, 
                MIN(price) AS min_price
            FROM Inventory
            GROUP BY store_id
        ) AS MinPrice 
        ON I2.store_id = MinPrice.store_id AND I2.price = MinPrice.min_price
    ) AS C 
    ON S.store_id = C.store_id

WHERE
    E.quantity < C.quantity
    
ORDER BY
    imbalance_ratio DESC,
    S.store_name ASC;
```