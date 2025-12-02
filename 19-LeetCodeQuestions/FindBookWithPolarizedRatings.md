# Find Books with Polarized Ratings

## Problem Statement

### Table: books

| Column Name | Type    | Description |
|-------------|---------|-------------|
| book_id     | int     | Unique identifier for each book |
| title       | varchar | Title of the book |
| author      | varchar | Author of the book |
| genre       | varchar | Genre of the book |
| pages       | int     | Number of pages in the book |

### Table: reading_sessions

| Column Name    | Type    | Description |
|----------------|---------|-------------|
| session_id     | int     | Unique identifier for each reading session |
| book_id        | int     | References books.book_id |
| reader_name    | varchar | Name of the reader |
| pages_read     | int     | Number of pages read in this session |
| session_rating | int     | Rating given by the reader (1-5) |

### Key Points:
- A book has polarized opinions if it has:
  - At least one rating ≥ 4 (high rating)
  - At least one rating ≤ 2 (low rating)
- Only consider books with at least 5 reading sessions
- Rating spread = highest_rating - lowest_rating
- Polarization score = (number of extreme ratings) / (total sessions)
  - Extreme ratings are those ≤ 2 or ≥ 4
- Only include books with polarization score ≥ 0.6 (60%)
- Order by polarization score (descending) and title (descending)

## Example

### Input

**books table:**
| book_id | title                  | author        | genre     | pages |
|---------|------------------------|---------------|-----------|-------|
| 1       | The Great Gatsby       | F. Scott      | Fiction   | 180   |
| 2       | To Kill a Mockingbird  | Harper Lee    | Fiction   | 281   |
| 3       | 1984                   | George Orwell | Dystopian | 328   |
| 4       | Pride and Prejudice    | Jane Austen   | Romance   | 432   |
| 5       | The Catcher in the Rye | J.D. Salinger | Fiction   | 277   |

**reading_sessions table:**
| session_id | book_id | reader_name | pages_read | session_rating |
|------------|---------|-------------|------------|----------------|
| 1          | 1       | Alice       | 50         | 5              |
| 2          | 1       | Bob         | 60         | 1              |
| 3          | 1       | Carol       | 40         | 4              |
| 4          | 1       | David       | 30         | 2              |
| 5          | 1       | Emma        | 45         | 5              |
| 6          | 2       | Frank       | 80         | 4              |
| 7          | 2       | Grace       | 70         | 4              |
| 8          | 2       | Henry       | 90         | 5              |
| 9          | 2       | Ivy         | 60         | 4              |
| 10         | 2       | Jack        | 75         | 4              |
| 11         | 3       | Kate        | 100        | 2              |
| 12         | 3       | Liam        | 120        | 1              |
| 13         | 3       | Mia         | 80         | 2              |
| 14         | 3       | Noah        | 90         | 1              |
| 15         | 3       | Olivia      | 110        | 4              |
| 16         | 3       | Paul        | 95         | 5              |
| 17         | 4       | Quinn       | 150        | 3              |
| 18         | 4       | Ruby        | 140        | 3              |
| 19         | 5       | Sam         | 80         | 1              |
| 20         | 5       | Tara        | 70         | 2              |

### Output
| book_id | title            | author        | genre     | pages | rating_spread | polarization_score |
|---------|------------------|---------------|-----------|-------|---------------|--------------------|
| 1       | The Great Gatsby | F. Scott      | Fiction   | 180   | 4             | 1.00               |
| 3       | 1984             | George Orwell | Dystopian | 328   | 4             | 1.00               |

## Solution

```sql
WITH book_stats AS (
    SELECT 
        b.book_id,
        b.title,
        b.author,
        b.genre,
        b.pages,
        COUNT(*) AS total_sessions,
        MAX(r.session_rating) AS highest_rating,
        MIN(r.session_rating) AS lowest_rating,
        SUM(CASE WHEN r.session_rating <= 2 OR r.session_rating >= 4 THEN 1 ELSE 0 END) AS extreme_ratings_count,
        SUM(CASE WHEN r.session_rating >= 4 THEN 1 ELSE 0 END) AS high_ratings_count,
        SUM(CASE WHEN r.session_rating <= 2 THEN 1 ELSE 0 END) AS low_ratings_count
    FROM 
        books b
    JOIN 
        reading_sessions r ON b.book_id = r.book_id
    GROUP BY 
        b.book_id, b.title, b.author, b.genre, b.pages
    HAVING 
        COUNT(*) >= 5  -- At least 5 reading sessions
        AND SUM(CASE WHEN r.session_rating >= 4 THEN 1 ELSE 0 END) > 0  -- At least one high rating
        AND SUM(CASE WHEN r.session_rating <= 2 THEN 1 ELSE 0 END) > 0  -- At least one low rating
)
SELECT 
    book_id,
    title,
    author,
    genre,
    pages,
    (highest_rating - lowest_rating) AS rating_spread,
    ROUND(extreme_ratings_count * 1.0 / total_sessions, 2) AS polarization_score
FROM 
    book_stats
WHERE 
    (extreme_ratings_count * 1.0 / total_sessions) >= 0.6  -- At least 60% extreme ratings
ORDER BY 
    polarization_score DESC,
    title DESC;

```


```sql
WITH book_ratings AS (
    SELECT 
        b.book_id,
        b.title,
        b.author,
        b.genre,
        b.pages,
        r.session_rating,
        COUNT(*) OVER (PARTITION BY b.book_id) AS total_sessions,
        MAX(r.session_rating) OVER (PARTITION BY b.book_id) AS highest_rating,
        MIN(r.session_rating) OVER (PARTITION BY b.book_id) AS lowest_rating,
        SUM(CASE WHEN r.session_rating <= 2 OR r.session_rating >= 4 THEN 1 ELSE 0 END) 
            OVER (PARTITION BY b.book_id) AS extreme_ratings_count
    FROM 
        books b
    JOIN 
        reading_sessions r ON b.book_id = r.book_id
),
book_stats AS (
    SELECT DISTINCT
        book_id,
        title,
        author,
        genre,
        pages,
        total_sessions,
        highest_rating,
        lowest_rating,
        extreme_ratings_count,
        ROUND(extreme_ratings_count * 1.0 / total_sessions, 2) AS polarization_score
    FROM 
        book_ratings
    WHERE 
        total_sessions >= 5
        AND highest_rating >= 4
        AND lowest_rating <= 2
)
SELECT 
    book_id,
    title,
    author,
    genre,
    pages,
    (highest_rating - lowest_rating) AS rating_spread,
    polarization_score
FROM 
    book_stats
WHERE 
    polarization_score >= 0.6
ORDER BY 
    polarization_score DESC,
    title DESC;


```