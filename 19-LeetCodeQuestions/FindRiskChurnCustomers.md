# Find Churn Risk Customers

## Problem Statement

### Table: subscription_events

| Column Name    | Type    | Description |
|----------------|---------|-------------|
| event_id       | int     | Unique identifier for each event |
| user_id        | int     | Identifier for the user |
| event_date     | date    | Date of the event |
| event_type     | varchar | Type of event (start, upgrade, downgrade, cancel) |
| plan_name      | varchar | Subscription plan (basic, standard, premium, or NULL for cancel) |
| monthly_amount | decimal | Monthly subscription cost after this event (0 for cancel) |

### Key Points:
A user is a **churn risk customer** if they meet ALL the following criteria:
1. Currently have an **active subscription** (last event is not 'cancel')
2. Have performed **at least one downgrade** in their subscription history
3. Current plan revenue is **less than 50%** of their historical maximum plan revenue
4. Have been a subscriber for **at least 60 days**

### Output:
- `user_id`
- `current_plan` (most recent non-cancel plan)
- `current_monthly_amount` (most recent non-zero monthly amount)
- `max_historical_amount` (highest monthly amount ever)
- `days_as_subscriber` (from first to last event)
- Ordered by `days_as_subscriber` (descending), then `user_id` (ascending)

## Example

### Input:

**subscription_events table:**
| event_id | user_id | event_date | event_type | plan_name | monthly_amount |
|----------|---------|------------|------------|-----------|----------------|
| 1        | 501     | 2024-01-01 | start      | premium   | 29.99          |
| 2        | 501     | 2024-02-15 | downgrade  | standard  | 19.99          |
| 3        | 501     | 2024-03-20 | downgrade  | basic     | 9.99           |
| 4        | 502     | 2024-01-05 | start      | standard  | 19.99          |
| 5        | 502     | 2024-02-10 | upgrade    | premium   | 29.99          |
| 6        | 502     | 2024-03-15 | downgrade  | basic     | 9.99           |
| 7        | 503     | 2024-01-10 | start      | basic     | 9.99           |
| 8        | 503     | 2024-02-20 | upgrade    | standard  | 19.99          |
| 9        | 503     | 2024-03-25 | upgrade    | premium   | 29.99          |
| 10       | 504     | 2024-01-15 | start      | premium   | 29.99          |
| 11       | 504     | 2024-03-01 | downgrade  | standard  | 19.99          |
| 12       | 504     | 2024-03-30 | cancel     | NULL      | 0.00           |
| 13       | 505     | 2024-02-01 | start      | basic     | 9.99           |
| 14       | 505     | 2024-02-28 | upgrade    | standard  | 19.99          |
| 15       | 506     | 2024-01-20 | start      | premium   | 29.99          |
| 16       | 506     | 2024-03-10 | downgrade  | basic     | 9.99           |

### Output:
| user_id | current_plan | current_monthly_amount | max_historical_amount | days_as_subscriber |
|---------|--------------|------------------------|-----------------------|--------------------|
| 501     | basic        | 9.99                   | 29.99                 | 79                 |
| 502     | basic        | 9.99                   | 29.99                 | 69                 |

## Solution

```sql
WITH user_metrics AS (
    SELECT 
        user_id,
        -- Get the first and last event dates
        MIN(event_date) AS first_event_date,
        MAX(event_date) AS last_event_date,
        -- Check if the last event was a cancellation
        MAX(CASE WHEN event_rank_desc = 1 AND event_type = 'cancel' THEN 1 ELSE 0 END) AS is_cancelled,
        -- Count downgrades
        SUM(CASE WHEN event_type = 'downgrade' THEN 1 ELSE 0 END) AS downgrade_count,
        -- Get current plan details (last non-cancel event)
        MAX(CASE WHEN event_rank_desc = 1 AND event_type != 'cancel' THEN plan_name END) AS current_plan,
        MAX(CASE WHEN event_rank_desc = 1 AND event_type != 'cancel' THEN monthly_amount END) AS current_monthly_amount,
        -- Get maximum historical amount
        MAX(monthly_amount) AS max_historical_amount
    FROM (
        SELECT 
            *,
            ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY event_date DESC, event_id DESC) AS event_rank_desc
        FROM 
            subscription_events
    ) ranked_events
    GROUP BY 
        user_id
    HAVING 
        -- Has at least one downgrade
        SUM(CASE WHEN event_type = 'downgrade' THEN 1 ELSE 0 END) > 0
        -- Has been a subscriber for at least 60 days
        AND DATEDIFF(MAX(event_date), MIN(event_date)) >= 60
)
SELECT 
    user_id,
    current_plan,
    current_monthly_amount,
    max_historical_amount,
    DATEDIFF(last_event_date, first_event_date) AS days_as_subscriber
FROM 
    user_metrics
WHERE 
    is_cancelled = 0  -- Not cancelled
    AND current_monthly_amount < max_historical_amount * 0.5  -- Current amount < 50% of max
ORDER BY 
    days_as_subscriber DESC,
    user_id ASC;


```

```sql


with constraint_list as (select user_id, 
        sum(case when  event_type = 'downgrade' then 1 else 0 end ) as downgrade_count, 
        case when max(event_date) then plan_name end as latest_subscription_plan,
        case when max(event_date) then monthly_amount end as current_monthly_amount,
        case when datediff(max(event_date), min(event_date)) >= 60 then datediff(max(event_date), min(event_date)) else 0 end as days_as_subscriber,
        max(monthly_amount) as  max_historical_amount
from subscription_events
group by user_id
)
select * from constraint_list
```