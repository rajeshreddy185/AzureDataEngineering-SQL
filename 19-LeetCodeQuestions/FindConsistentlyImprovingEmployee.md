# Find Consistently Improving Employees

## Problem Statement

### Table: employees

| Column Name | Type    | Description |
|-------------|---------|-------------|
| employee_id | int     | Unique identifier for each employee |
| name        | varchar | Employee's name |

### Table: performance_reviews

| Column Name | Type | Description |
|-------------|------|-------------|
| review_id   | int  | Unique identifier for each review |
| employee_id | int  | References employees.employee_id |
| review_date | date | Date of the performance review |
| rating      | int  | Rating from 1 (poor) to 5 (excellent) |

### Key Points:
- An employee must have at least 3 reviews to be considered
- We only look at the most recent 3 reviews (by date) for each employee
- The ratings in these 3 reviews must be strictly increasing
- Calculate improvement score as (latest_rating - earliest_rating)

## Example

### Input

**employees table:**
| employee_id | name           |
|-------------|----------------|
| 1           | Alice Johnson  |
| 2           | Bob Smith      |
| 3           | Carol Davis    |
| 4           | David Wilson   |
| 5           | Emma Brown     |

**performance_reviews table:**
| review_id | employee_id | review_date | rating |
|-----------|-------------|-------------|--------|
| 1         | 1           | 2023-01-15  | 2      |
| 2         | 1           | 2023-04-15  | 3      |
| 3         | 1           | 2023-07-15  | 4      |
| 4         | 1           | 2023-10-15  | 5      |
| 5         | 2           | 2023-02-01  | 3      |
| 6         | 2           | 2023-05-01  | 2      |
| 7         | 2           | 2023-08-01  | 4      |
| 8         | 2           | 2023-11-01  | 5      |
| 9         | 3           | 2023-03-10  | 1      |
| 10        | 3           | 2023-06-10  | 2      |
| 11        | 3           | 2023-09-10  | 3      |
| 12        | 3           | 2023-12-10  | 4      |
| 13        | 4           | 2023-01-20  | 4      |
| 14        | 4           | 2023-04-20  | 4      |
| 15        | 4           | 2023-07-20  | 4      |
| 16        | 5           | 2023-02-15  | 3      |
| 17        | 5           | 2023-05-15  | 2      |

### Output
| employee_id | name          | improvement_score |
|-------------|---------------|-------------------|
| 2           | Bob Smith     | 3                 |
| 1           | Alice Johnson | 2                 |
| 3           | Carol Davis   | 2                 |

## Solution
-- Assign row numbers for each employee's reviews, most recent first
-- Get the three most recent ratings
-- Check for strictly increasing ratings
-- Ensure all three ratings exist

```sql
WITH RankedReviews AS (
    SELECT 
        pr.employee_id,
        e.name,
        pr.rating,
        pr.review_date,
        ROW_NUMBER() OVER (
            PARTITION BY pr.employee_id 
            ORDER BY pr.review_date DESC
        ) AS rn
    FROM 
        performance_reviews pr
    JOIN 
        employees e ON pr.employee_id = e.employee_id
),
LastThreeReviews AS (
    SELECT 
        employee_id,
        name,
        MAX(CASE WHEN rn = 1 THEN rating END) AS latest_rating,
        MAX(CASE WHEN rn = 2 THEN rating END) AS second_rating,
        MAX(CASE WHEN rn = 3 THEN rating END) AS third_rating
    FROM 
        RankedReviews
    WHERE 
        rn <= 3
    GROUP BY 
        employee_id, name
    HAVING 
        COUNT(*) = 3
)
SELECT 
    employee_id,
    name,
    (latest_rating - third_rating) AS improvement_score
FROM 
    LastThreeReviews
WHERE 
    third_rating < second_rating 
    AND second_rating < latest_rating
ORDER BY 
    improvement_score DESC,
    name ASC;
```

