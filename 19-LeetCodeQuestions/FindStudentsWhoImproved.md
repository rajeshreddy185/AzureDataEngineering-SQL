# Find Students Who Improved Their Scores

## Problem Statement

### Table: Scores

| Column Name | Type    | Description |
|-------------|---------|-------------|
| student_id  | int     | Student identifier |
| subject     | varchar | Subject name |
| score       | int     | Exam score (0-100) |
| exam_date   | varchar | Date of exam in 'YYYY-MM-DD' format |

### Key Points:
- `(student_id, subject, exam_date)` is the primary key
- Each row represents a student's score in a subject on a specific date
- Need to identify students who improved in a subject across multiple attempts

## Task

Find students who have shown improvement in a subject, where:
1. They have taken the same subject on at least two different dates
2. Their latest score is higher than their first score

Return the result with:
- student_id
- subject
- first_score (their first score in that subject)
- latest_score (their most recent score in that subject)

## Example

### Input

**Scores table:**
| student_id | subject | score | exam_date  |
|------------|---------|-------|------------|
| 101        | Math    | 70    | 2023-01-15 |
| 101        | Math    | 85    | 2023-02-15 |
| 101        | Physics | 65    | 2023-01-15 |
| 101        | Physics | 60    | 2023-02-15 |
| 102        | Math    | 80    | 2023-01-15 |
| 102        | Math    | 85    | 2023-02-15 |
| 103        | Math    | 90    | 2023-01-15 |
| 104        | Physics | 75    | 2023-01-15 |
| 104        | Physics | 85    | 2023-02-15 |

### Output
| student_id | subject | first_score | latest_score |
|------------|---------|-------------|--------------|
| 101        | Math    | 70          | 85           |
| 102        | Math    | 80          | 85           |
| 104        | Physics | 75          | 85           |

## Solution

```sql
WITH FirstScores AS (
    SELECT 
        student_id,
        subject,
        score AS first_score
    FROM (
        SELECT 
            student_id,
            subject,
            score,
            ROW_NUMBER() OVER (PARTITION BY student_id, subject ORDER BY exam_date) as rn
        FROM 
            Scores
    ) t
    WHERE rn = 1
),
LatestScores AS (
    SELECT 
        student_id,
        subject,
        score AS latest_score
    FROM (
        SELECT 
            student_id,
            subject,
            score,
            ROW_NUMBER() OVER (PARTITION BY student_id, subject ORDER BY exam_date DESC) as rn
        FROM 
            Scores
    ) t
    WHERE rn = 1
),
StudentSubjectAttempts AS (
    SELECT 
        student_id,
        subject,
        COUNT(*) as attempt_count
    FROM 
        Scores
    GROUP BY 
        student_id, subject
    HAVING 
        COUNT(*) >= 2
)
SELECT 
    f.student_id,
    f.subject,
    f.first_score,
    l.latest_score
FROM 
    FirstScores f
JOIN 
    LatestScores l ON f.student_id = l.student_id AND f.subject = l.subject
JOIN 
    StudentSubjectAttempts s ON f.student_id = s.student_id AND f.subject = s.subject
WHERE 
    l.latest_score > f.first_score
ORDER BY 
    f.student_id, f.subject;
```


```sql

WITH MinMaxDt AS (
    SELECT student_id, 
    subject, 
    MIN(exam_date) as min_dt, 
    MAX(exam_date) as max_dt
FROM Score
GROU BY student_id, subject
),

MinScore AS (
    SELECT 
        s.subject_id,
        s.subject,
        s.exam_date,
        s.score 
    FROM 
       Scores as s
       JOIN MinMaxDt as m
       ON s.student_id = m.student_id
       AND
       s.exam_date = m.min_dt
       AND
       s.subject = m.subject
),

MaxScore AS (
    SELECT 
        s.subject_id,
        s.subject,
        s.exam_date,
        s.score 
    FROM 
       Scores as s
       JOIN MinMaxDt as m
       ON s.student_id = m.student_id
       AND
       s.exam_date = m.max_dt
       AND
       s.subject = m.subject
),

SELECT 
    m1.student_id, 
    m1.subject,
    m1.score AS first_score,
    m2.score AS latest_score
FROM 
    MinScore AS m1
    JOIN 
    MaxScore AS m2
    ON m1.student_id = m2.student_id and
    m1.subject = m2.subject
WHERE m1.score < m2.score
ORDER BY m1.student_id, m1.subject

```