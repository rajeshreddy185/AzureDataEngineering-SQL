# Machine Processing Time Analysis

## Problem Description

### Table: Activity

| Column Name   | Type    |
|---------------|---------|
| machine_id    | int     |
| process_id    | int     |
| activity_type | enum    |
| timestamp     | float   |

- `activity_type` is an ENUM of type ('start', 'end').
- Each row represents a process's start or end time on a machine.
- `(machine_id, process_id, activity_type)` is a composite primary key.

## Task

Calculate the average processing time for each machine, where processing time is the difference between 'end' and 'start' timestamps for each process.

Return the result table with:
- `machine_id`
- `processing_time` (rounded to 3 decimal places)

Order the result by `machine_id` in ascending order.

## Example

### Input:

**Activity table:**
| machine_id | process_id | activity_type | timestamp |
|------------|------------|---------------|-----------|
| 0          | 0          | start         | 0.712     |
| 0          | 0          | end           | 1.520     |
| 0          | 1          | start         | 3.140     |
| 0          | 1          | end           | 4.120     |
| 1          | 0          | start         | 0.550     |
| 1          | 0          | end           | 1.550     |
| 1          | 1          | start         | 0.430     |
| 1          | 1          | end           | 1.420     |
| 2          | 0          | start         | 4.100     |
| 2          | 0          | end           | 4.512     |
| 2          | 1          | start         | 2.500     |
| 2          | 1          | end           | 5.000     |

### Output:
| machine_id | processing_time |
|------------|-----------------|
| 0          | 0.894           |
| 1          | 0.995           |
| 2          | 1.456           |

### Explanation:
- **Machine 0**: ((1.520 - 0.712) + (4.120 - 3.140)) / 2 = 0.894
- **Machine 1**: ((1.550 - 0.550) + (1.420 - 0.430)) / 2 = 0.995
- **Machine 2**: ((4.512 - 4.100) + (5.000 - 2.500)) / 2 = 1.456

### Solution:
Note: cannot directly subtract a CASE expression from another CASE expression like that in the SELECT clause to find the duration.
Conditional Aggregation: For each unique pair of (machine_id, process_id) within the outer grouping:

MAX(CASE WHEN A.activity_type = 'end' THEN A.timestamp ELSE NULL END): This finds the timestamp where 
the activity_type is 'end'. Since there's only one 'end' time per process, MAX() simply returns that value.

```sql
SELECT
    A.machine_id,
    ROUND(
        SUM(
            MAX(CASE WHEN A.activity_type = 'end' THEN A.timestamp ELSE NULL END)
            -
            MIN(CASE WHEN A.activity_type = 'start' THEN A.timestamp ELSE NULL END)
        )
        /
        COUNT(DISTINCT A.process_id),
    3) AS processing_time
FROM
    Activity AS A
GROUP BY
    A.machine_id;
```

```sql
SELECT 
    a.machine_id,
    ROUND(AVG(b.timestamp - a.timestamp), 3) AS processing_time
FROM 
    Activity a
JOIN 
    Activity b ON a.machine_id = b.machine_id 
    AND a.process_id = b.process_id
    AND a.activity_type = 'start' 
    AND b.activity_type = 'end'
GROUP BY 
    a.machine_id
ORDER BY 
    a.machine_id;
```