# Capital Gain/Loss

## Problem Statement

### Table: Stocks

| Column Name    | Type    | Description |
|----------------|---------|-------------|
| stock_name     | varchar | Name of the stock |
| operation      | enum    | Either 'Buy' or 'Sell' |
| operation_day  | int     | Day of the operation |
| price          | int     | Price at which stock was bought/sold |

### Key Points:
- `(stock_name, operation_day)` is the primary key
- Each 'Sell' has a corresponding 'Buy' on a previous day
- Each 'Buy' has a corresponding 'Sell' on a future day
- Need to calculate net capital gain/loss for each stock

## Task

Calculate the total capital gain/loss for each stock, where:
- Capital gain/loss = Sum of (Sell price - Buy price) for each buy-sell pair
- Results should be ordered by stock name

## Example

### Input

**Stocks table:**
| stock_name   | operation | operation_day | price  |
|--------------|-----------|---------------|--------|
| Leetcode     | Buy       | 1             | 1000   |
| Corona Masks | Buy       | 2             | 10     |
| Leetcode     | Sell      | 5             | 9000   |
| Handbags     | Buy       | 17            | 30000  |
| Corona Masks | Sell      | 3             | 1010   |
| Corona Masks | Buy       | 4             | 1000   |
| Corona Masks | Sell      | 5             | 500    |
| Corona Masks | Buy       | 6             | 1000   |
| Handbags     | Sell      | 29            | 7000   |
| Corona Masks | Sell      | 10            | 10000  |

### Output
| stock_name   | capital_gain_loss |
|--------------|-------------------|
| Corona Masks | 9500              |
| Handbags     | -23000            |
| Leetcode     | 8000              |

## Solution

```sql
WITH BuyOperations AS (
    SELECT 
        stock_name,
        operation_day,
        price,
        ROW_NUMBER() OVER (PARTITION BY stock_name ORDER BY operation_day) as rn
    FROM 
        Stocks
    WHERE 
        operation = 'Buy'
),
SellOperations AS (
    SELECT 
        stock_name,
        operation_day,
        price,
        ROW_NUMBER() OVER (PARTITION BY stock_name ORDER BY operation_day) as rn
    FROM 
        Stocks
    WHERE 
        operation = 'Sell'
)
SELECT 
    b.stock_name,
    SUM(s.price - b.price) AS capital_gain_loss
FROM 
    BuyOperations b
JOIN 
    SellOperations s 
    ON b.stock_name = s.stock_name 
    AND b.rn = s.rn
GROUP BY 
    b.stock_name
ORDER BY 
    b.stock_name;
```




```sql

SELECT 
    stock_name,
    SUM(
        CASE 
            WHEN operation = 'Sell' THEN price
            ELSE -price
        END
    ) AS capital_gain_loss
FROM 
    Stocks
GROUP BY 
    stock_name
ORDER BY 
    stock_name;

```