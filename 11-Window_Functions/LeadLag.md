# Exchange Seats

## Problem Description

### Table: Seat

| Column Name | Type    | Description |
|-------------|---------|-------------|
| id          | int     | Primary key |
| student     | varchar | Student name |

**Notes:**
- `id` is the primary key
- IDs start from 1 and increment continuously
- Each row represents a student and their seat ID

## Task

Swap the seat ID of every two consecutive students. If the number of students is odd, the last student's seat remains unchanged.

Return the result ordered by `id` in ascending order.

## Example

### Input:

**Seat table:**
```
+----+---------+
| id | student |
+----+---------+
| 1  | Abbot   |
| 2  | Doris   |
| 3  | Emerson |
| 4  | Green   |
| 5  | Jeames  |
+----+---------+
```

### Output:
```
+----+---------+
| id | student |
+----+---------+
| 1  | Doris   |
| 2  | Abbot   |
| 3  | Green   |
| 4  | Emerson |
| 5  | Jeames  |
+----+---------+
```

### Explanation:
- Students with even IDs (2, 4) swap with the student before them (1, 3)
- The last student (id=5) remains in place as there's no student after to swap with

## Solution:

Here we use lead , lag to get the previous and next column values in query
check for the id if it is odd number then using lead we get the student name of next row
in case if the id is odd and no next row then it will return null
in else we are getting previous student name using lag

NOTE: LEAD, LAG we mush use with order by to define the order it should consider the previous
and next rows (partition by is optional if we want to apply on subset of columns without combining like group by)


### Using CASE with LEAD and LAG:
```sql
SELECT 
    id,
    CASE 
        WHEN id % 2 = 1 THEN coalesce(LEAD(student, 1) OVER (ORDER BY id), student)
        ELSE LAG(student, 1) OVER (ORDER BY id)
    END AS student
FROM 
    Seat
ORDER BY 
    id;
```

### Using CASE with self-join:
```sql
SELECT 
    s1.id,
    COALESCE(s2.student, s1.student) AS student
FROM 
    Seat s1
LEFT JOIN 
    Seat s2 ON (s1.id % 2 = 1 AND s1.id + 1 = s2.id) OR 
               (s1.id % 2 = 0 AND s1.id - 1 = s2.id)
ORDER BY 
    s1.id;
```

### Using UNION with CASE:
```sql
-- For even IDs
SELECT 
    id - 1 AS id, 
    student 
FROM 
    Seat 
WHERE 
    id % 2 = 0

UNION ALL

-- For odd IDs that have a next ID
SELECT 
    id + 1 AS id, 
    student 
FROM 
    Seat 
WHERE 
    id % 2 = 1 
    AND id < (SELECT MAX(id) FROM Seat)

UNION ALL

-- For the last odd ID (when total count is odd)
SELECT 
    id, 
    student 
FROM 
    Seat 
WHERE 
    id % 2 = 1 
    AND id = (SELECT MAX(id) FROM Seat)

ORDER BY 
    id;
```

### Notes:
- The first solution using window functions (LEAD/LAG) is the most elegant and efficient
- The second solution uses a self-join to find the adjacent students
- The third solution demonstrates how to handle the logic using UNION ALL
- The results are ordered by ID as required
