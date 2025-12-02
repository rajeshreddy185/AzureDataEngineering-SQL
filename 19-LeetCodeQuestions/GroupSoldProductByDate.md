# Group Sold Products By The Date

## Problem Statement

### Table: Activities

| Column Name | Type    | Description |
|-------------|---------|-------------|
| sell_date   | date    | Date of sale |
| product     | varchar | Product name |

### Key Points:
- No primary key (may contain duplicates)
- Each row represents a product sold on a specific date
- Need to group by date and aggregate product information

## Task

For each date, find:
1. The number of different products sold (`num_sold`)
2. A comma-separated list of product names (`products`), sorted lexicographically

## Example

### Input

**Activities table:**
| sell_date  | product    |
|------------|------------|
| 2020-05-30 | Headphone  |
| 2020-06-01 | Pencil     |
| 2020-06-02 | Mask       |
| 2020-05-30 | Basketball |
| 2020-06-01 | Bible      |
| 2020-06-02 | Mask       |
| 2020-05-30 | T-Shirt    |

### Output
| sell_date  | num_sold | products                     |
|------------|----------|------------------------------|
| 2020-05-30 | 3        | Basketball,Headphone,T-shirt |
| 2020-06-01 | 2        | Bible,Pencil                 |
| 2020-06-02 | 1        | Mask                         |

## Solution

```sql
SELECT 
    sell_date,
    COUNT(DISTINCT product) AS num_sold,
    GROUP_CONCAT(DISTINCT product ORDER BY product SEPARATOR ',') AS products
FROM 
    Activities
GROUP BY 
    sell_date
ORDER BY 
    sell_date;
```

```sql

SELECT 
    sell_date,
    COUNT(DISTINCT product) AS num_sold,
    STRING_AGG(product, ',') WITHIN GROUP (ORDER BY product) AS products
FROM 
    (SELECT DISTINCT sell_date, product FROM Activities) t
GROUP BY 
    sell_date
ORDER BY 
    sell_date;

```