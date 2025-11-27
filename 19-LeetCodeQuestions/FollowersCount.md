# User Followers Count

## Problem Description

### Table: Followers

| Column Name | Type |
|-------------|------|
| user_id     | int  |
| follower_id | int  |

- `(user_id, follower_id)` is the composite primary key.
- Each row indicates that the user with `follower_id` follows the user with `user_id`.

## Task

For each user, count the number of their followers.

Return the result ordered by `user_id` in ascending order.

## Example

### Input:

**Followers table:**
| user_id | follower_id |
|---------|-------------|
| 0       | 1           |
| 1       | 0           |
| 2       | 0           |
| 2       | 1           |

### Output:
| user_id | followers_count |
|---------|-----------------|
| 0       | 1               |
| 1       | 1               |
| 2       | 2               |

### Explanation:
- User 0 has 1 follower (user 1)
- User 1 has 1 follower (user 0)
- User 2 has 2 followers (users 0 and 1)

### Solution:

```sql
SELECT 
    user_id,
    COUNT(follower_id) AS followers_count
FROM 
    Followers
GROUP BY 
    user_id
ORDER BY 
    user_id;


