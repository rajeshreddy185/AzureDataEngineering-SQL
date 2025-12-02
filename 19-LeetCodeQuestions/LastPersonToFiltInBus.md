# Last Person to Fit in the Bus

## Problem Statement

### Table: Queue

| Column Name | Type    | Description |
|-------------|---------|-------------|
| person_id   | int     | Unique identifier |
| person_name | varchar | Name of the person |
| weight      | int     | Weight in kilograms |
| turn        | int     | Boarding order (1 = first to board) |

### Key Points:
- `person_id` is unique
- `turn` values range from 1 to n (number of rows)
- People board the bus in order of their `turn`
- Bus has a weight limit of 1000 kg
- First person will always be able to board

## Task

Find the name of the last person who can board the bus without exceeding the weight limit.

## Example

### Input

**Queue table:**
| person_id | person_name | weight | turn |
|-----------|-------------|--------|------|
| 5         | Alice       | 250    | 1    |
| 4         | Bob         | 175    | 5    |
| 3         | Alex        | 350    | 2    |
| 6         | John Cena   | 400    | 3    |
| 1         | Winston     | 500    | 6    |
| 2         | Marie       | 200    | 4    |

### Output
| person_name |
|-------------|
| John Cena   |

### Explanation:
- Alice (250 kg) boards first
- Alex (350 kg) boards next (total: 600 kg)
- John Cena (400 kg) boards next (total: 1000 kg)
- Marie cannot board as it would exceed the limit (1200 kg > 1000 kg)
- John Cena is the last person who could board

## Solution

```sql
WITH CumulativeWeights AS (
    SELECT 
        person_name,
        SUM(weight) OVER (ORDER BY turn) AS running_total
    FROM 
        Queue
)
SELECT 
    person_name
FROM 
    CumulativeWeights
WHERE 
    running_total <= 1000
ORDER BY 
    running_total DESC
LIMIT 1;
```


```sql

WITH CumulativeQueue AS (
    SELECT 
        person_name,
        turn,
        SUM(weight) OVER(
        ORDER BY turn
        ROWS BETWEEN UNBOUNDED PRECEEDING AND CURRENT ROW
        ) AS running_weight
    FROM 
        Queue
)

SELECT 
    person_name
FROM
    CumulativeQueue
WHERE
    running_weight <= 1000
ORDER BY 
    turn DESC
LIMIT 1;

```