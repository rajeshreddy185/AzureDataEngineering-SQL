# Number of Unique Subjects Taught by Each Teacher

## Problem Description

### Table: Teacher

| Column Name | Type | Description |
|-------------|------|-------------|
| teacher_id  | int  | Teacher identifier |
| subject_id  | int  | Subject identifier |
| dept_id     | int  | Department identifier |

**Note:** (subject_id, dept_id) is the primary key of this table.

## Task

Write a solution to calculate the number of unique subjects each teacher teaches in the university. Return the result table in any order.

## Example

### Input:

**Teacher table:**
| teacher_id | subject_id | dept_id |
|------------|------------|---------|
| 1          | 2          | 3       |
| 1          | 2          | 4       |
| 1          | 3          | 3       |
| 2          | 1          | 1       |
| 2          | 2          | 1       |
| 2          | 3          | 1       |
| 2          | 4          | 1       |

### Output:
| teacher_id | cnt |
|------------|-----|
| 1          | 2   |
| 2          | 4   |

### Explanation:
- Teacher 1 teaches 2 unique subjects: subject 2 and subject 3
- Teacher 2 teaches 4 unique subjects: subjects 1, 2, 3, and 4

## Solution:

### Using COUNT with DISTINCT:
```sql
SELECT 
    teacher_id,
    COUNT(DISTINCT subject_id) AS cnt
FROM 
    Teacher
GROUP BY 
    teacher_id
ORDER BY 
    teacher_id;
```

### Alternative using subquery:
```sql
SELECT 
    t.teacher_id,
    (SELECT COUNT(DISTINCT subject_id) 
     FROM Teacher 
     WHERE teacher_id = t.teacher_id) AS cnt
FROM 
    (SELECT DISTINCT teacher_id FROM Teacher) t
ORDER BY 
    t.teacher_id;
```

### Notes:
- The first solution is more efficient as it requires only one pass through the table
- We use COUNT(DISTINCT subject_id) to count only unique subjects per teacher
- The GROUP BY clause groups the results by teacher_id
- The result is ordered by teacher_id for consistent output
