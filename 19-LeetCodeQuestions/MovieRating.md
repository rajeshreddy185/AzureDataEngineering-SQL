# Movie Rating

## Problem Statement

### Table: Movies

| Column Name | Type    | Description |
|-------------|---------|-------------|
| movie_id    | int     | Primary Key |
| title       | varchar | Movie title |

### Table: Users

| Column Name | Type    | Description |
|-------------|---------|-------------|
| user_id     | int     | Primary Key |
| name        | varchar | User name (unique) |

### Table: MovieRating

| Column Name | Type    | Description |
|-------------|---------|-------------|
| movie_id    | int     | Foreign Key to Movies |
| user_id     | int     | Foreign Key to Users |
| rating     | int     | Rating (1-5) |
| created_at | date    | Date of rating |

### Task

1. Find the user who has rated the greatest number of movies
   - In case of a tie, return the lexicographically smaller name
2. Find the movie with the highest average rating in February 2020
   - In case of a tie, return the lexicographically smaller title

## Example

### Input

**Movies table:**

| movie_id | title    |
|----------|----------|
| 1        | Avengers |
| 2        | Frozen 2 |
| 3        | Joker    |

**Users table:**

| user_id | name   |
|---------|--------|
| 1       | Daniel |
| 2       | Monica |
| 3       | Maria  |
| 4       | James  |

**MovieRating table:**

| movie_id | user_id | rating | created_at |
|----------|---------|--------|------------|
| 1        | 1       | 3      | 2020-01-12 |
| 1        | 2       | 4      | 2020-02-11 |
| 1        | 3       | 2      | 2020-02-12 |
| 1        | 4       | 1      | 2020-01-01 |
| 2        | 1       | 5      | 2020-02-17 |
| 2        | 2       | 2      | 2020-02-01 |
| 2        | 3       | 2      | 2020-03-01 |
| 3        | 1       | 3      | 2020-02-22 |
| 3        | 2       | 4      | 2020-02-25 |

### Output
| results  |
|----------|
| Daniel   |
| Frozen 2 |

## Solution

```sql
WITH UserRatingCounts AS (
    SELECT 
        u.name,
        COUNT(*) AS rating_count
    FROM 
        MovieRating mr
    JOIN 
        Users u ON mr.user_id = u.user_id
    GROUP BY 
        u.user_id, u.name
    ORDER BY 
        rating_count DESC, 
        u.name
    LIMIT 1
),
MovieAvgRatings AS (
    SELECT 
        m.title,
        AVG(mr.rating) AS avg_rating
    FROM 
        MovieRating mr
    JOIN 
        Movies m ON mr.movie_id = m.movie_id
    WHERE 
        mr.created_at BETWEEN '2020-02-01' AND '2020-02-29'
    GROUP BY 
        m.movie_id, m.title
    ORDER BY 
        avg_rating DESC, 
        m.title
    LIMIT 1
)

SELECT name AS results FROM UserRatingCounts
UNION ALL
SELECT title AS results FROM MovieAvgRatings;
```*