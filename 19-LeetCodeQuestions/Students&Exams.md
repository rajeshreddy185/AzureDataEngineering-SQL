# Students & Examinations

## Problem Description


#### Students
| Column Name  | Type    |
|--------------|---------|
| student_id   | int     |
| student_name | varchar |

- `student_id` is the primary key (column with unique values) for this table.
- Each row contains the ID and name of one student in the school.

#### Subjects
| Column Name  | Type    |
|--------------|---------|
| subject_name | varchar |

- `subject_name` is the primary key (column with unique values) for this table.
- Each row contains the name of one subject in the school.

#### Examinations
| Column Name  | Type    |
|--------------|---------|
| student_id   | int     |
| subject_name | varchar |

- There is no primary key for this table (may contain duplicates).
- Each student from the Students table takes every course from the Subjects table.
- Each row indicates that a student with ID `student_id` attended the exam of `subject_name`.

## Task

Write a solution to find the number of times each student attended each exam.

Return the result table ordered by `student_id` and `subject_name`.

## Example

### Input:

**Students table:**

| student_id | student_name |
|------------|--------------|
| 1          | Alice        |
| 2          | Bob          |
| 13         | John         |
| 6          | Alex         |

**Subjects table:**

| subject_name |
|--------------|
| Math         |
| Physics      |
| Programming  |

**Examinations table:**

| student_id | subject_name |
|------------|--------------|
| 1          | Math         |
| 1          | Physics      |
| 1          | Programming  |
| 2          | Programming  |
| 1          | Physics      |
| 1          | Math         |
| 13         | Math         |
| 13         | Programming  |
| 13         | Physics      |
| 2          | Math         |
| 1          | Math         |

### Output:
| student_id | student_name | subject_name | attended_exams |
|------------|--------------|--------------|----------------|
| 1          | Alice        | Math         | 3              |
| 1          | Alice        | Physics      | 2              |
| 1          | Alice        | Programming  | 1              |
| 2          | Bob          | Math         | 1              |
| 2          | Bob          | Physics      | 0              |
| 2          | Bob          | Programming  | 1              |
| 6          | Alex         | Math         | 0              |
| 6          | Alex         | Physics      | 0              |
| 6          | Alex         | Programming  | 0              |
| 13         | John         | Math         | 1              |
| 13         | John         | Physics      | 1              |
| 13         | John         | Programming  | 1              |

### Explanation:
- Alice attended Math 3 times, Physics 2 times, and Programming 1 time.
- Bob attended Math 1 time, Programming 1 time, and did not attend Physics.
- Alex did not attend any exams.
- John attended Math, Physics, and Programming once each.


```sql

SELECT 
    s.student_id,
    s.student_name,
    sub.subject_name
FROM 
    Students as s
CROSS JOIN
    Subjects as sub
LEFT JOIN 
    Examination as e
    ON s.student_id = e.student_id
    AND sub.subject_name = e.subject_name
GROUP BY 
    s.student_name, s.student_is, sub.subject_name
ORDER BY s.student_id, sub.subject_name

```