# Find Golden Hour Customers

## Problem Statement

### Table: restaurant_orders

| Column Name      | Type     | Description |
|------------------|----------|-------------|
| order_id         | int      | Unique identifier for each order |
| customer_id      | int      | Identifier for the customer |
| order_timestamp  | datetime | Date and time of the order |
| order_amount     | decimal  | Total amount of the order |
| payment_method   | varchar  | Payment method (cash, card, or app) |
| order_rating     | int      | Rating given by customer (1-5, NULL if not rated) |

### Key Points:
A customer is a **golden hour customer** if they meet ALL the following criteria:
1. Made **at least 3 orders**
2. At least **60% of orders** are during peak hours (11:00-14:00 or 18:00-21:00)
3. Average rating for rated orders is at least 4.0 (rounded to 2 decimal places)
4. Have rated at least 50% of their orders

### Output:
- `customer_id`
- `total_orders` (total number of orders)
- `peak_hour_percentage` (percentage of orders during peak hours)
- `average_rating` (average of all rated orders, rounded to 2 decimal places)
- Ordered by `average_rating` (descending), then by `customer_id` (descending)

## Example

### Input:

**restaurant_orders table:**
| order_id | customer_id | order_timestamp     | order_amount | payment_method | order_rating |
|----------|-------------|---------------------|--------------|----------------|--------------|
| 1        | 101         | 2024-03-01 12:30:00 | 25.50        | card           | 5            |
| 2        | 101         | 2024-03-02 19:15:00 | 32.00        | app            | 4            |
| 3        | 101         | 2024-03-03 13:45:00 | 28.75        | card           | 5            |
| 4        | 101         | 2024-03-04 20:30:00 | 41.00        | app            | NULL         |
| 5        | 102         | 2024-03-01 11:30:00 | 18.50        | cash           | 4            |
| 6        | 102         | 2024-03-02 12:00:00 | 22.00        | card           | 3            |
| 7        | 102         | 2024-03-03 15:30:00 | 19.75        | cash           | NULL         |
| 8        | 103         | 2024-03-01 19:00:00 | 55.00        | app            | 5            |
| 9        | 103         | 2024-03-02 20:45:00 | 48.50        | app            | 4            |
| 10       | 103         | 2024-03-03 18:30:00 | 62.00        | card           | 5            |
| 11       | 104         | 2024-03-01 10:00:00 | 15.00        | cash           | 3            |
| 12       | 104         | 2024-03-02 09:30:00 | 18.00        | cash           | 2            |
| 13       | 104         | 2024-03-03 16:00:00 | 20.00        | card           | 3            |
| 14       | 105         | 2024-03-01 12:15:00 | 30.00        | app            | 4            |
| 15       | 105         | 2024-03-02 13:00:00 | 35.50        | app            | 5            |
| 16       | 105         | 2024-03-03 11:45:00 | 28.00        | card           | 4            |

### Output:
| customer_id | total_orders | peak_hour_percentage | average_rating |
|-------------|--------------|----------------------|----------------|
| 103         | 3            | 100                  | 4.67           |
| 101         | 4            | 100                  | 4.67           |
| 105         | 3            | 100                  | 4.33           |

## Solution

```sql
WITH customer_metrics AS (
    SELECT 
        customer_id,
        COUNT(*) AS total_orders,
        ROUND(100.0 * SUM(
            CASE 
                WHEN HOUR(order_timestamp) BETWEEN 11 AND 13  -- 11:00-13:59
                OR HOUR(order_timestamp) BETWEEN 18 AND 20    -- 18:00-20:59
                THEN 1 
                ELSE 0 
            END
        ) / COUNT(*), 0) AS peak_hour_percentage,
        ROUND(AVG(order_rating), 2) AS average_rating,
        SUM(CASE WHEN order_rating IS NOT NULL THEN 1 ELSE 0 END) AS rated_orders
    FROM 
        restaurant_orders
    GROUP BY 
        customer_id
    HAVING 
        COUNT(*) >= 3
        AND 100.0 * SUM(
            CASE 
                WHEN HOUR(order_timestamp) BETWEEN 11 AND 13 
                OR HOUR(order_timestamp) BETWEEN 18 AND 20 
                THEN 1 
                ELSE 0 
            END
        ) / COUNT(*) >= 60
        AND SUM(CASE WHEN order_rating IS NOT NULL THEN 1 ELSE 0 END) * 2 >= COUNT(*)
)
SELECT 
    customer_id,
    total_orders,
    peak_hour_percentage,
    average_rating
FROM 
    customer_metrics
WHERE 
    average_rating >= 4.0
ORDER BY 
    average_rating DESC,
    customer_id DESC;


