# Borrowed Books with No Available Copies

## Problem Description

### Table: library_books

| Column Name      | Type    | Description |
|------------------|---------|-------------|
| book_id          | int     | Unique identifier |
| title            | varchar | Book title |
| author           | varchar | Author name |
| genre            | varchar | Book genre |
| publication_year | int     | Year of publication |
| total_copies     | int     | Total copies owned by the library |

### Table: borrowing_records

| Column Name   | Type    | Description |
|---------------|---------|-------------|
| record_id     | int     | Unique identifier |
| book_id       | int     | References library_books.book_id |
| borrower_name | varchar | Name of the borrower |
| borrow_date   | date    | Date when the book was borrowed |
| return_date   | date    | NULL if the book is still borrowed |

## Task

Find all books that are:
1. Currently borrowed (not returned, i.e., return_date IS NULL)
2. Have zero available copies in the library

Return the result ordered by:
1. Number of current borrowers (descending)
2. Book title (ascending)

## Example

### Input:

**library_books table:**
```
+---------+------------------------+------------------+----------+------------------+--------------+
| book_id | title                  | author           | genre    | publication_year | total_copies |
+---------+------------------------+------------------+----------+------------------+--------------+
| 1       | The Great Gatsby       | F. Scott         | Fiction  | 1925             | 3            |
| 2       | To Kill a Mockingbird  | Harper Lee       | Fiction  | 1960             | 3            |
| 3       | 1984                   | George Orwell    | Dystopian| 1949             | 1            |
| 4       | Pride and Prejudice    | Jane Austen      | Romance  | 1813             | 2            |
| 5       | The Catcher in the Rye | J.D. Salinger    | Fiction  | 1951             | 1            |
| 6       | Brave New World        | Aldous Huxley    | Dystopian| 1932             | 4            |
+---------+------------------------+------------------+----------+------------------+--------------+
```

**borrowing_records table:**
```
+-----------+---------+---------------+-------------+-------------+
| record_id | book_id | borrower_name | borrow_date | return_date |
+-----------+---------+---------------+-------------+-------------+
| 1         | 1       | Alice Smith   | 2024-01-15  | NULL        |
| 2         | 1       | Bob Johnson   | 2024-01-20  | NULL        |
| 3         | 2       | Carol White   | 2024-01-10  | 2024-01-25  |
| 4         | 3       | David Brown   | 2024-02-01  | NULL        |
| 5         | 4       | Emma Wilson   | 2024-01-05  | NULL        |
| 6         | 5       | Frank Davis   | 2024-01-18  | 2024-02-10  |
| 7         | 1       | Grace Miller  | 2024-02-05  | NULL        |
| 8         | 6       | Henry Taylor  | 2024-01-12  | NULL        |
| 9         | 2       | Ivan Clark    | 2024-02-12  | NULL        |
| 10        | 2       | Jane Adams    | 2024-02-15  | NULL        |
+-----------+---------+---------------+-------------+-------------+
```

### Output:
```
+---------+------------------+---------------+-----------+------------------+-------------------+
| book_id | title            | author        | genre     | publication_year | current_borrowers |
+---------+------------------+---------------+-----------+------------------+-------------------+
| 1       | The Great Gatsby | F. Scott      | Fiction   | 1925             | 3                 | 
| 3       | 1984             | George Orwell | Dystopian | 1949             | 1                 |
+---------+------------------+---------------+-----------+------------------+-------------------+
```

### Explanation:
- **The Great Gatsby**: Total copies = 3, Currently borrowed by 3 people (Alice, Bob, Grace) → Available = 0
- **1984**: Total copies = 1, Currently borrowed by 1 person (David) → Available = 0
- Other books have at least one copy available.
- Results are ordered by current_borrowers (descending) and then by title (ascending).

## Solution:

### Using GROUP BY and HAVING:
```sql
WITH current_borrowals AS (
    SELECT 
        b.book_id,
        b.title,
        b.author,
        b.genre,
        b.publication_year,
        b.total_copies,
        COUNT(br.record_id) AS current_borrowers
    FROM 
        library_books b
    LEFT JOIN 
        borrowing_records br ON b.book_id = br.book_id 
                            AND br.return_date IS NULL
    GROUP BY 
        b.book_id, b.title, b.author, b.genre, b.publication_year, b.total_copies
    HAVING 
        COUNT(br.record_id) = b.total_copies
        AND COUNT(br.record_id) > 0
)
SELECT 
    book_id,
    title,
    author,
    genre,
    publication_year,
    current_borrowers
FROM 
    current_borrowals
ORDER BY 
    current_borrowers DESC,
    title ASC;
```

### Using a subquery for current borrowers:
```sql
SELECT 
    b.book_id,
    b.title,
    b.author,
    b.genre,
    b.publication_year,
    COUNT(br.record_id) AS current_borrowers
FROM 
    library_books b
INNER JOIN (
    SELECT book_id, record_id 
    FROM borrowing_records 
    WHERE return_date IS NULL
) br ON b.book_id = br.book_id
GROUP BY 
    b.book_id, b.title, b.author, b.genre, b.publication_year, b.total_copies
HAVING 
    COUNT(br.record_id) = b.total_copies
ORDER BY 
    current_borrowers DESC,
    b.title ASC;
```

### Using a window function (for databases that support it):
```sql
WITH book_borrowals AS (
    SELECT 
        b.*,
        COUNT(CASE WHEN br.return_date IS NULL THEN 1 END) OVER (PARTITION BY b.book_id) AS current_borrowers
    FROM 
        library_books b
    LEFT JOIN 
        borrowing_records br ON b.book_id = br.book_id
)
SELECT DISTINCT
    book_id,
    title,
    author,
    genre,
    publication_year,
    current_borrowers
FROM 
    book_borrowals
WHERE 
    current_borrowers = total_copies
    AND current_borrowers > 0
ORDER BY 
    current_borrowers DESC,
    title ASC;
```

### Notes:
- The first solution uses a CTE to calculate current borrowers and then filters for books where all copies are borrowed
- The second solution uses a subquery to first filter for only currently borrowed books
- The third solution demonstrates using a window function for a different approach
- All solutions ensure we only return books where the number of current borrowings equals the total copies available
