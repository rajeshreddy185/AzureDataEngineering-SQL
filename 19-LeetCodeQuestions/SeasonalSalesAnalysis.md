# Seasonal Product Category Analysis

## Problem Statement

### Table: sales

| Column Name | Type    | Description |
|-------------|---------|-------------|
| sale_id     | int     | Unique identifier for each sale |
| product_id  | int     | Product identifier |
| sale_date   | date    | Date of sale |
| quantity    | int     | Number of units sold |
| price       | decimal | Price per unit |

### Table: products

| Column Name  | Type    | Description |
|--------------|---------|-------------|
| product_id   | int     | Product identifier |
| product_name | varchar | Name of the product |
| category     | varchar | Product category |

### Seasons:
- **Winter**: December, January, February
- **Spring**: March, April, May
- **Summer**: June, July, August
- **Fall**: September, October, November

### Key Points:
- Need to determine the most popular product category for each season
- Popularity is based on total quantity sold
- In case of a tie, the category with higher total revenue wins
- Results should be ordered by season

## Example

### Input

**sales table:**
| sale_id | product_id | sale_date  | quantity | price |
|---------|------------|------------|----------|-------|
| 1       | 1          | 2023-01-15 | 5        | 10.00 |
| 2       | 2          | 2023-01-20 | 4        | 15.00 |
| 3       | 3          | 2023-03-10 | 3        | 18.00 |
| 4       | 4          | 2023-04-05 | 1        | 20.00 |
| 5       | 1          | 2023-05-20 | 2        | 10.00 |
| 6       | 2          | 2023-06-12 | 4        | 15.00 |
| 7       | 5          | 2023-06-15 | 5        | 12.00 |
| 8       | 3          | 2023-07-24 | 2        | 18.00 |
| 9       | 4          | 2023-08-01 | 5        | 20.00 |
| 10      | 5          | 2023-09-03 | 3        | 12.00 |
| 11      | 1          | 2023-09-25 | 6        | 10.00 |
| 12      | 2          | 2023-11-10 | 4        | 15.00 |
| 13      | 3          | 2023-12-05 | 6        | 18.00 |
| 14      | 4          | 2023-12-22 | 3        | 20.00 |
| 15      | 5          | 2024-02-14 | 2        | 12.00 |

**products table:**
| product_id | product_name | category |
|------------|--------------|----------|
| 1          | Warm Jacket  | Apparel  |
| 2          | Designer Jeans| Apparel  |
| 3          | Cutting Board| Kitchen  |
| 4          | Smart Speaker| Tech     |
| 5          | Yoga Mat     | Fitness  |

### Output
| season | category | total_quantity | total_revenue |
|--------|----------|----------------|---------------|
| Fall   | Apparel  | 10             | 120.00        |
| Spring | Kitchen  | 3              | 54.00         |
| Summer | Tech     | 5              | 100.00        |
| Winter | Apparel  | 9              | 110.00        |

## Solution

```sql
WITH SeasonalSales AS (
    SELECT 
        s.*,
        p.category,
        s.quantity * s.price AS revenue,
        CASE 
            WHEN MONTH(s.sale_date) IN (12, 1, 2) THEN 'Winter'
            WHEN MONTH(s.sale_date) BETWEEN 3 AND 5 THEN 'Spring'
            WHEN MONTH(s.sale_date) BETWEEN 6 AND 8 THEN 'Summer'
            WHEN MONTH(s.sale_date) BETWEEN 9 AND 11 THEN 'Fall'
        END AS season
    FROM 
        sales s
    JOIN 
        products p ON s.product_id = p.product_id
),
CategoryStats AS (
    SELECT 
        season,
        category,
        SUM(quantity) AS total_quantity,
        ROUND(SUM(revenue), 2) AS total_revenue,
        ROW_NUMBER() OVER (
            PARTITION BY season 
            ORDER BY 
                SUM(quantity) DESC,
                SUM(revenue) DESC
        ) AS rn
    FROM 
        SeasonalSales
    GROUP BY 
        season, category
)
SELECT 
    season,
    category,
    total_quantity,
    total_revenue
FROM 
    CategoryStats
WHERE 
    rn = 1
ORDER BY 
    FIELD(season, 'Winter', 'Spring', 'Summer', 'Fall');