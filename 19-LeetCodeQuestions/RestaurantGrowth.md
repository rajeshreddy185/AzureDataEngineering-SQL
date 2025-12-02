# Restaurant Growth

## Problem Statement

### Table: Customer

| Column Name | Type    | Description |
|-------------|---------|-------------|
| customer_id | int     | Customer identifier |
| name        | varchar | Customer name |
| visited_on  | date    | Date of visit |
| amount      | int     | Amount spent by the customer |

### Key Points:
- `(customer_id, visited_on)` is the primary key
- There is at least one customer every day
- Need to calculate a 7-day moving average (current day + 6 previous days)
- Round average_amount to 2 decimal places
- Order results by `visited_on` in ascending order

## Task

Compute the moving average of customer payments over a 7-day window for each day where we have at least 7 days of data.

## Example

### Input

**Customer table:**
| customer_id | name   | visited_on | amount |
|-------------|--------|------------|--------|
| 1           | Jhon   | 2019-01-01 | 100    |
| 2           | Daniel | 2019-01-02 | 110    |
| 3           | Jade   | 2019-01-03 | 120    |
| 4           | Khaled | 2019-01-04 | 130    |
| 5           | Winston| 2019-01-05 | 110    |
| 6           | Elvis  | 2019-01-06 | 140    |
| 7           | Anna   | 2019-01-07 | 150    |
| 8           | Maria  | 2019-01-08 | 80     |
| 9           | Jaze   | 2019-01-09 | 110    |
| 1           | Jhon   | 2019-01-10 | 130    |
| 3           | Jade   | 2019-01-10 | 150    |

### Output
| visited_on | amount | average_amount |
|------------|--------|----------------|
| 2019-01-07 | 860    | 122.86         |
| 2019-01-08 | 840    | 120.00         |
| 2019-01-09 | 840    | 120.00         |
| 2019-01-10 | 1000   | 142.86         |

## Solution

```sql
WITH DailyTotals AS (
    SELECT 
        visited_on,
        SUM(amount) AS daily_amount
    FROM 
        Customer
    GROUP BY 
        visited_on
),
MovingAverages AS (
    SELECT 
        a.visited_on,
        SUM(b.daily_amount) AS amount,
        ROUND(AVG(b.daily_amount), 2) AS average_amount,
        ROW_NUMBER() OVER (ORDER BY a.visited_on) AS row_num
    FROM 
        DailyTotals a
    JOIN 
        DailyTotals b 
        ON DATEDIFF(a.visited_on, b.visited_on) BETWEEN 0 AND 6
    GROUP BY 
        a.visited_on
    HAVING 
        COUNT(b.visited_on) = 7
)
SELECT 
    visited_on,
    amount,
    average_amount
FROM 
    MovingAverages
ORDER BY 
    visited_on;
```


