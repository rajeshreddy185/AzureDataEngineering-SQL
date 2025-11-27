# Top Travellers

## Problem Description

### Tables

#### Users
| Column Name | Type    |
|-------------|---------|
| id          | int     |
| name        | varchar |

- `id` is the column with unique values for this table.
- `name` is the name of the user.

#### Rides
| Column Name | Type |
|-------------|------|
| id          | int  |
| user_id     | int  |
| distance    | int  |

- `id` is the column with unique values for this table.
- `user_id` is the id of the user who traveled the distance.

## Task

Write a solution to report the distance traveled by each user, sorted by:
1. `travelled_distance` in descending order
2. `name` in ascending order (if distances are equal)

## Example

### Input:

**Users table:**

| id | name     |
|----|----------|
| 1  | Alice    |
| 2  | Bob      |
| 3  | Alex     |
| 4  | Donald   |
| 7  | Lee      |
| 13 | Jonathan |
| 19 | Elvis    |

**Rides table:**

| id | user_id | distance |
|----|---------|----------|
| 1  | 1       | 120      |
| 2  | 2       | 317      |
| 3  | 3       | 222      |
| 4  | 7       | 100      |
| 5  | 13      | 312      |
| 6  | 19      | 50       |
| 7  | 7       | 120      |
| 8  | 19      | 400      |
| 9  | 7       | 230      |

### Output:
| name     | travelled_distance |
|----------|--------------------|
| Elvis    | 450                |
| Lee      | 450                |
| Bob      | 317                |
| Jonathan | 312                |
| Alex     | 222                |
| Alice    | 120                |
| Donald   | 0                  |

### Explanation:
- Elvis and Lee both traveled 450 miles total. Elvis is listed first because 'Elvis' comes before 'Lee' alphabetically.
- Bob, Jonathan, Alex, and Alice each have one ride, ordered by their total distance.
- Donald has no rides, so his distance is 0.

### Solution:

```sql
SELECT 
    u.name,
    COALESCE(SUM(r.distance), 0) AS travelled_distance
FROM 
    Users u
LEFT JOIN 
    Rides r ON u.id = r.user_id
GROUP BY 
    u.id, u.name
ORDER BY 
    travelled_distance DESC,
    u.name ASC;