# Recyclable and Low Fat Products

## Problem Description

### Table: Products

| Column Name | Type | Description |
|-------------|------|-------------|
| product_id  | int  | Primary key |
| low_fats    | enum | 'Y' for low fat, 'N' otherwise |
| recyclable  | enum | 'Y' for recyclable, 'N' otherwise |

## Task

Find the IDs of products that are both:
- Low fat (`low_fats = 'Y'`)
- Recyclable (`recyclable = 'Y'`)

Return the result in any order.

## Example

### Input:

**Products table:**
| product_id | low_fats | recyclable |
|------------|----------|------------|
| 0          | Y        | N          |
| 1          | Y        | Y          |
| 2          | N        | Y          |
| 3          | Y        | Y          |
| 4          | N        | N          |

### Output:
| product_id |
|------------|
| 1          |
| 3          |

### Explanation:
- Product 1: Low fat (Y) and Recyclable (Y) → Included
- Product 3: Low fat (Y) and Recyclable (Y) → Included
- Other products don't meet both conditions

## Solution:

```sql
SELECT 
    product_id
FROM 
    Products
WHERE 
    low_fats = 'Y' 
    AND recyclable = 'Y'
ORDER BY 
    product_id;
```

```sql
SELECT 
    p1.product_id
FROM 
    Products p1
INNER JOIN 
    Products p2 
    ON p1.product_id = p2.product_id
    AND p1.low_fats = 'Y'
    AND p2.recyclable = 'Y';