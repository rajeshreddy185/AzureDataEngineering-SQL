# Employee Office Time Tracking

## Problem Description

### Table: Employees

| Column Name | Type |
|-------------|------|
| emp_id      | int  |
| event_day   | date |
| in_time     | int  |
| out_time    | int  |

- `(emp_id, event_day, in_time)` is the composite primary key.
- `event_day` is the date of the event.
- `in_time` and `out_time` are in minutes (1-1440).
- No overlapping time entries for the same employee on the same day.
- `in_time` is always less than `out_time`.

## Task

For each employee and each day, calculate the total time spent in the office by summing up the differences between `out_time` and `in_time` for all their entries that day.

Return the result in any order.

## Example

### Input:

**Employees table:**
| emp_id | event_day  | in_time | out_time |
|--------|------------|---------|----------|
| 1      | 2020-11-28 | 4       | 32       |
| 1      | 2020-11-28 | 55      | 200      |
| 1      | 2020-12-03 | 1       | 42       |
| 2      | 2020-11-28 | 3       | 33       |
| 2      | 2020-12-09 | 47      | 74       |

### Output:
| day        | emp_id | total_time |
|------------|--------|------------|
| 2020-11-28 | 1      | 173        |
| 2020-11-28 | 2      | 30         |
| 2020-12-03 | 1      | 41         |
| 2020-12-09 | 2      | 27         |

### Explanation:
- Employee 1 on 2020-11-28: (32-4) + (200-55) = 28 + 145 = 173 minutes
- Employee 2 on 2020-11-28: 33-3 = 30 minutes
- Employee 1 on 2020-12-03: 42-1 = 41 minutes
- Employee 2 on 2020-12-09: 74-47 = 27 minutes

### Solution:

```sql
SELECT 
    event_day AS day,
    emp_id,
    SUM(out_time - in_time) AS total_time
FROM 
    Employees
GROUP BY 
    event_day, emp_id
ORDER BY 
    event_day, emp_id;