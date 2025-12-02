# Find Overbooked Employees

## Problem Statement

### Table: employees

| Column Name   | Type    | Description |
|---------------|---------|-------------|
| employee_id   | int     | Unique identifier for each employee |
| employee_name | varchar | Employee's name |
| department    | varchar | Department name |

### Table: meetings

| Column Name    | Type    | Description |
|----------------|---------|-------------|
| meeting_id     | int     | Unique identifier for each meeting |
| employee_id    | int     | References employees.employee_id |
| meeting_date   | date    | Date of the meeting |
| meeting_type   | varchar | Type: 'Team', 'Client', or 'Training' |
| duration_hours | decimal | Duration of the meeting in hours |

### Key Points:
- Standard work week: 40 hours
- Meeting-heavy threshold: >20 hours per week (50% of standard week)
- Week is defined as Monday to Sunday
- Only include employees who were meeting-heavy for at least 2 weeks
- Order by number of meeting-heavy weeks (descending) and then by employee name (ascending)

## Example

### Input

**employees table:**
| employee_id | employee_name  | department  |
|-------------|----------------|-------------|
| 1           | Alice Johnson  | Engineering |
| 2           | Bob Smith      | Marketing   |
| 3           | Carol Davis    | Sales       |
| 4           | David Wilson   | Engineering |
| 5           | Emma Brown     | HR          |

**meetings table:**
| meeting_id | employee_id | meeting_date | meeting_type | duration_hours |
|------------|-------------|--------------|--------------|----------------|
| 1          | 1           | 2023-06-05   | Team         | 8.0            |
| 2          | 1           | 2023-06-06   | Client       | 6.0            |
| 3          | 1           | 2023-06-07   | Training     | 7.0            |
| 4          | 1           | 2023-06-12   | Team         | 12.0           |
| 5          | 1           | 2023-06-13   | Client       | 9.0            |
| 6          | 2           | 2023-06-05   | Team         | 15.0           |
| 7          | 2           | 2023-06-06   | Client       | 8.0            |
| 8          | 2           | 2023-06-12   | Training     | 10.0           |
| 9          | 3           | 2023-06-05   | Team         | 4.0            |
| 10         | 3           | 2023-06-06   | Client       | 3.0            |
| 11         | 4           | 2023-06-05   | Team         | 25.0           |
| 12         | 4           | 2023-06-19   | Client       | 22.0           |
| 13         | 5           | 2023-06-05   | Training     | 2.0            |

### Output
| employee_id | employee_name  | department  | meeting_heavy_weeks |
|-------------|----------------|-------------|---------------------|
| 1           | Alice Johnson  | Engineering | 2                   |
| 4           | David Wilson   | Engineering | 2                   |

## Solution

```sql
WITH weekly_meeting_hours AS (
    -- Calculate total meeting hours per employee per week
    SELECT 
        m.employee_id,
        e.employee_name,
        e.department,
        DATE_ADD(
            m.meeting_date, 
            INTERVAL -WEEKDAY(m.meeting_date) DAY
        ) AS week_start_date,
        SUM(m.duration_hours) AS total_hours
    FROM 
        meetings m
    JOIN 
        employees e ON m.employee_id = e.employee_id
    GROUP BY 
        m.employee_id, 
        e.employee_name, 
        e.department,
        DATE_ADD(m.meeting_date, INTERVAL -WEEKDAY(m.meeting_date) DAY)
    HAVING 
        SUM(m.duration_hours) > 20  -- More than 20 hours is meeting-heavy
),
meeting_heavy_weeks AS (
    -- Count meeting-heavy weeks per employee
    SELECT 
        employee_id,
        employee_name,
        department,
        COUNT(*) AS meeting_heavy_weeks
    FROM 
        weekly_meeting_hours
    GROUP BY 
        employee_id, employee_name, department
    HAVING 
        COUNT(*) >= 2  -- At least 2 meeting-heavy weeks
)
-- Final result with ordering
SELECT 
    employee_id,
    employee_name,
    department,
    meeting_heavy_weeks
FROM 
    meeting_heavy_weeks
ORDER BY 
    meeting_heavy_weeks DESC,
    employee_name ASC;