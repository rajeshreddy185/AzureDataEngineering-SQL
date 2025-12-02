# Analyze Subscription Conversion

## Problem Statement

### Table: UserActivity

| Column Name      | Type    | Description |
|------------------|---------|-------------|
| user_id          | int     | User identifier |
| activity_date    | date    | Date of activity |
| activity_type    | varchar | Type of activity ('free_trial', 'paid', 'cancelled') |
| activity_duration| int     | Minutes spent on platform |

### Key Points:
- `(user_id, activity_date, activity_type)` is the primary key
- Each row represents a user's daily activity
- Users start with a 7-day free trial, then can convert to paid or cancel

## Task

For users who converted from free trial to paid subscription, calculate:
1. Average daily activity duration during free trial period
2. Average daily activity duration during paid subscription period

Only include users who had both free_trial and paid activities.

## Example

### Input

**UserActivity table:**
| user_id | activity_date | activity_type | activity_duration |
|---------|---------------|---------------|-------------------|
| 1       | 2023-01-01    | free_trial    | 45                |
| 1       | 2023-01-02    | free_trial    | 30                |
| 1       | 2023-01-05    | free_trial    | 60                |
| 1       | 2023-01-10    | paid          | 75                |
| 1       | 2023-01-12    | paid          | 90                |
| 1       | 2023-01-15    | paid          | 65                |
| 2       | 2023-02-01    | free_trial    | 55                |
| 2       | 2023-02-03    | free_trial    | 25                |
| 2       | 2023-02-07    | free_trial    | 50                |
| 2       | 2023-02-10    | cancelled     | 0                 |
| 3       | 2023-03-05    | free_trial    | 70                |
| 3       | 2023-03-06    | free_trial    | 60                |
| 3       | 2023-03-08    | free_trial    | 80                |
| 3       | 2023-03-12    | paid          | 50                |
| 3       | 2023-03-15    | paid          | 55                |
| 3       | 2023-03-20    | paid          | 85                |
| 4       | 2023-04-01    | free_trial    | 40                |
| 4       | 2023-04-03    | free_trial    | 35                |
| 4       | 2023-04-05    | paid          | 45                |
| 4       | 2023-04-07    | cancelled     | 0                 |

### Output
| user_id | trial_avg_duration | paid_avg_duration |
|---------|--------------------|-------------------|
| 1       | 45.00              | 76.67             |
| 3       | 70.00              | 63.33             |
| 4       | 37.50              | 45.00             |

## Solution

```sql
WITH UserConversion AS (
    -- Users who have both free_trial and paid activities
    SELECT 
        user_id
    FROM 
        UserActivity
    WHERE 
        activity_type IN ('free_trial', 'paid')
    GROUP BY 
        user_id
    HAVING 
        COUNT(DISTINCT activity_type) = 2
),
TrialStats AS (
    -- Calculate average trial duration
    SELECT 
        ua.user_id,
        ROUND(AVG(ua.activity_duration), 2) AS trial_avg_duration
    FROM 
        UserActivity ua
    JOIN 
        UserConversion uc ON ua.user_id = uc.user_id
    WHERE 
        ua.activity_type = 'free_trial'
    GROUP BY 
        ua.user_id
),
PaidStats AS (
    -- Calculate average paid duration
    SELECT 
        ua.user_id,
        ROUND(AVG(ua.activity_duration), 2) AS paid_avg_duration
    FROM 
        UserActivity ua
    JOIN 
        UserConversion uc ON ua.user_id = uc.user_id
    WHERE 
        ua.activity_type = 'paid'
    GROUP BY 
        ua.user_id
)
-- Combine the results
SELECT 
    t.user_id,
    t.trial_avg_duration,
    p.paid_avg_duration
FROM 
    TrialStats t
JOIN 
    PaidStats p ON t.user_id = p.user_id
ORDER BY 
    t.user_id;

```

```sql

SELECT 
    user_id,
    ROUND(AVG(CASE WHEN activity_type = 'free_trial' THEN activity_duration END), 2) AS trial_avg_duration,
    ROUND(AVG(CASE WHEN activity_type = 'paid' THEN activity_duration END), 2) AS paid_avg_duration
FROM 
    UserActivity
WHERE 
    user_id IN (
        SELECT user_id 
        FROM UserActivity 
        WHERE activity_type = 'paid'
        INTERSECT
        SELECT user_id 
        FROM UserActivity 
        WHERE activity_type = 'free_trial'
    )
    AND activity_type IN ('free_trial', 'paid')
GROUP BY 
    user_id
ORDER BY 
    user_id;
    
    
```

```sql
WITH Converters AS (
    SELECT
        user_id
    FROM
        UserActivity
    WHERE
        activity_type IN ('free_trial', 'paid')
    GROUP BY
        user_id
    HAVING
        SUM(CASE WHEN activity_type = 'free_trial' THEN 1 ELSE 0 END) > 0 AND
        SUM(CASE WHEN activity_type = 'paid' THEN 1 ELSE 0 END) > 0
),
UserMetrics AS (
    SELECT
        UA.user_id,
            SUM(CASE WHEN UA.activity_type = 'free_trial' THEN UA.activity_duration ELSE 0 END) AS total_trial_duration,        
        SUM(CASE WHEN UA.activity_type = 'paid' THEN UA.activity_duration ELSE 0 END) AS total_paid_duration,
        COUNT(DISTINCT CASE WHEN UA.activity_type = 'free_trial' THEN UA.activity_date END) AS total_trial_days,        
        COUNT(DISTINCT CASE WHEN UA.activity_type = 'paid' THEN UA.activity_date END) AS total_paid_days
    FROM 
        UserActivity UA
    JOIN
        Converters C ON UA.user_id = C.user_id 
    WHERE 
        UA.activity_type IN ('free_trial', 'paid') 
    GROUP BY 
        UA.user_id
)

SELECT
    user_id,
    ROUND(
        total_trial_duration / NULLIF(total_trial_days, 0),
        2
    ) AS trial_avg_duration,
    
    ROUND(
        total_paid_duration / NULLIF(total_paid_days, 0),
        2
    ) AS paid_avg_duration
FROM
    UserMetrics
ORDER BY
    user_id;
```