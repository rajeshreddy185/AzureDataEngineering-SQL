# Who Has the Most Friends

## Problem Description

### Table: RequestAccepted

| Column Name   | Type | Description |
|---------------|------|-------------|
| requester_id  | int  | ID of the user who sent the friend request |
| accepter_id   | int  | ID of the user who received the request |
| accept_date   | date | Date when the request was accepted |

**Note:** (requester_id, accepter_id) is the primary key for this table.

## Task

Find the person who has the most friends and the number of friends they have. 

**Follow-up:** Handle cases where multiple people could have the same maximum number of friends.

## Example

### Input:

**RequestAccepted table:**
```
+--------------+-------------+-------------+
| requester_id | accepter_id | accept_date |
+--------------+-------------+-------------+
| 1            | 2           | 2016/06/03  |
| 1            | 3           | 2016/06/08  |
| 2            | 3           | 2016/06/08  |
| 3            | 4           | 2016/06/09  |
+--------------+-------------+-------------+
```

### Output:
```
+----+-----+
| id | num |
+----+-----+
| 3  | 3   |
+----+-----+
```

### Explanation:
- User 3 is friends with users 1, 2, and 4
- No other user has more than 2 friends

## Solution:


### 

```sql
SELECT 
    d.id, 
    SUM(d.requester_count) AS num 
FROM (
    SELECT 
        requester_id AS id, 
        COUNT(*) AS requester_count 
    FROM 
        RequestAccepted
    GROUP BY 
        requester_id
    
    UNION ALL
    
    SELECT 
        accepter_id AS id, 
        COUNT(*) AS requester_count
    FROM
        RequestAccepted
    GROUP BY 
        accepter_id
) AS d 
GROUP BY 
    d.id
ORDER BY 
    SUM(d.requester_count) DESC
LIMIT 1;

```




### Basic Solution (Single Winner):
```sql
WITH friend_counts AS (
    SELECT 
        user_id,
        COUNT(*) AS friend_count
    FROM (
        SELECT requester_id AS user_id FROM RequestAccepted
        UNION ALL
        SELECT accepter_id AS user_id FROM RequestAccepted
    ) all_friends
    GROUP BY user_id
    ORDER BY friend_count DESC
    LIMIT 1
)
SELECT 
    user_id AS id,
    friend_count AS num
FROM 
    friend_counts;
```

### Follow-up Solution (Handling Ties):
```sql
WITH friend_counts AS (
    SELECT 
        user_id,
        COUNT(*) AS friend_count
    FROM (
        SELECT requester_id AS user_id FROM RequestAccepted
        UNION ALL
        SELECT accepter_id AS user_id FROM RequestAccepted
    ) all_friends
    GROUP BY user_id
),
max_friends AS (
    SELECT MAX(friend_count) AS max_count
    FROM friend_counts
)
SELECT 
    fc.user_id AS id,
    fc.friend_count AS num
FROM 
    friend_counts fc
JOIN 
    max_friends mf ON fc.friend_count = mf.max_count
ORDER BY 
    id;
```

### Alternative Solution Using Window Function:
```sql
WITH friend_counts AS (
    SELECT 
        user_id,
        COUNT(*) AS friend_count,
        DENSE_RANK() OVER (ORDER BY COUNT(*) DESC) AS rnk
    FROM (
        SELECT requester_id AS user_id FROM RequestAccepted
        UNION ALL
        SELECT accepter_id AS user_id FROM RequestAccepted
    ) all_friends
    GROUP BY user_id
)
SELECT 
    user_id AS id,
    friend_count AS num
FROM 
    friend_counts
WHERE 
    rnk = 1
ORDER BY 
    user_id;
```

### Notes:
1. The solution combines both `requester_id` and `accepter_id` to get all friend relationships
2. The basic solution assumes there's only one person with the maximum friends
3. The follow-up solution handles cases where multiple people might have the same maximum number of friends
4. The window function approach provides a clean way to handle ties using `DENSE_RANK()`
5. All solutions order the results by user ID for consistent output