# Find Drivers with Improved Fuel Efficiency

## Problem Statement

### Table: drivers

| Column Name | Type    | Description |
|-------------|---------|-------------|
| driver_id   | int     | Unique identifier for each driver |
| driver_name | varchar | Driver's name |

### Table: trips

| Column Name   | Type    | Description |
|---------------|---------|-------------|
| trip_id       | int     | Unique identifier for each trip |
| driver_id     | int     | References drivers.driver_id |
| trip_date     | date    | Date of the trip |
| distance_km   | decimal | Distance traveled in kilometers |
| fuel_consumed | decimal | Amount of fuel consumed in liters |

### Key Points:
- Calculate fuel efficiency as `distance_km / fuel_consumed` for each trip
- First half of year: January to June
- Second half of year: July to December
- Only include drivers with trips in both halves of the year
- Calculate improvement as (second_half_avg - first_half_avg)
- Round all results to 2 decimal places
- Order by efficiency improvement (descending) and then by driver name (ascending)

## Example

### Input

**drivers table:**
| driver_id | driver_name   |
|-----------|---------------|
| 1         | Alice Johnson |
| 2         | Bob Smith     |
| 3         | Carol Davis   |
| 4         | David Wilson  |
| 5         | Emma Brown    |

**trips table:**
| trip_id | driver_id | trip_date  | distance_km | fuel_consumed |
|---------|-----------|------------|-------------|---------------|
| 1       | 1         | 2023-02-15 | 120.5       | 10.2          |
| 2       | 1         | 2023-03-20 | 200.0       | 16.5          |
| 3       | 1         | 2023-08-10 | 150.0       | 11.0          |
| 4       | 1         | 2023-09-25 | 180.0       | 12.5          |
| 5       | 2         | 2023-01-10 | 100.0       | 9.0           |
| 6       | 2         | 2023-04-15 | 250.0       | 22.0          |
| 7       | 2         | 2023-10-05 | 200.0       | 15.0          |
| 8       | 3         | 2023-03-12 | 80.0        | 8.5           |
| 9       | 3         | 2023-05-18 | 90.0        | 9.2           |
| 10      | 4         | 2023-07-22 | 160.0       | 12.8          |
| 11      | 4         | 2023-11-30 | 140.0       | 11.0          |
| 12      | 5         | 2023-02-28 | 110.0       | 11.5          |

### Output
| driver_id | driver_name   | first_half_avg | second_half_avg | efficiency_improvement |
|-----------|---------------|----------------|-----------------|------------------------|
| 2         | Bob Smith     | 11.24          | 13.33           | 2.10                   |
| 1         | Alice Johnson | 11.97          | 14.02           | 2.05                   |

## Solution
-- Must have trips in both halves


```sql
WITH TripEfficiency AS (
    SELECT 
        t.driver_id,
        d.driver_name,
        t.trip_date,
        t.distance_km / t.fuel_consumed AS efficiency,
        CASE 
            WHEN MONTH(t.trip_date) BETWEEN 1 AND 6 THEN 'First Half'
            ELSE 'Second Half'
        END AS half_year
    FROM 
        trips t
    JOIN 
        drivers d ON t.driver_id = d.driver_id
),
DriverHalves AS (
    SELECT 
        driver_id,
        driver_name,
        ROUND(AVG(CASE WHEN half_year = 'First Half' THEN efficiency END), 2) AS first_half_avg,
        ROUND(AVG(CASE WHEN half_year = 'Second Half' THEN efficiency END), 2) AS second_half_avg
    FROM 
        TripEfficiency
    GROUP BY 
        driver_id, driver_name
    HAVING 
        COUNT(DISTINCT half_year) = 2  
)
SELECT 
    driver_id,
    driver_name,
    first_half_avg,
    second_half_avg,
    ROUND(second_half_avg - first_half_avg, 2) AS efficiency_improvement
FROM 
    DriverHalves
WHERE 
    second_half_avg > first_half_avg  -- Only show improvements
ORDER BY 
    efficiency_improvement DESC,
    driver_name ASC;
```

```sql

WITH TripEfficiency AS (
    SELECT 
        t.driver_id,
        d.driver_name,
        t.distance_km / t.fuel_consumed AS efficiency,
        CASE 
            WHEN MONTH(t.trip_date) BETWEEN 1 AND 6 THEN 'first_half'
            ELSE 'second_half'
        END AS half_year
    FROM 
        trips t
    JOIN 
        drivers d ON t.driver_id = d.driver_id
),
PivotedEfficiency AS (
    SELECT 
        driver_id,
        driver_name,
        ROUND(AVG(CASE WHEN half_year = 'first_half' THEN efficiency END), 2) AS first_half_avg,
        ROUND(AVG(CASE WHEN half_year = 'second_half' THEN efficiency END), 2) AS second_half_avg
    FROM 
        TripEfficiency
    GROUP BY 
        driver_id, driver_name
    HAVING 
        COUNT(DISTINCT half_year) = 2
)
SELECT 
    driver_id,
    driver_name,
    first_half_avg,
    second_half_avg,
    ROUND(second_half_avg - first_half_avg, 2) AS efficiency_improvement
FROM 
    PivotedEfficiency
WHERE 
    second_half_avg > first_half_avg
ORDER BY 
    efficiency_improvement DESC,
    driver_name ASC;

```
-- First CTE: Calculate fuel efficiency for each trip in first and second halves
-- First half (Jan-Jun) calculations
-- Second half (Jul-Dec) calculations
-- Second CTE: Calculate average efficiency for each driver in both halves
-- Final result with driver details and ordering
-- Only show improvements
-- Secondary sort by name for ties

```sql

WITH trip_efficiency AS (
    SELECT 
        driver_id,
        trip_id, 
        
        SUM(CASE 
                WHEN trip_date BETWEEN '2023-01-01' AND '2023-06-30' 
                THEN distance_km / fuel_consumed 
                ELSE 0 
            END) AS fh_fuel_efficiency,
        SUM(CASE 
                WHEN trip_date BETWEEN '2023-01-01' AND '2023-06-30' 
                THEN 1 
                ELSE 0 
            END) AS fh_trip_count,
       
        SUM(CASE 
                WHEN trip_date BETWEEN '2023-07-01' AND '2023-12-31' 
                THEN distance_km / fuel_consumed 
                ELSE 0 
            END) AS sh_fuel_efficiency,
        SUM(CASE 
                WHEN trip_date BETWEEN '2023-07-01' AND '2023-12-31' 
                THEN 1 
                ELSE 0 
            END) AS sh_trip_count
    FROM 
        trips
    GROUP BY 
        driver_id, trip_id
),


overall_efficiency AS (
    SELECT 
        driver_id,
        CASE 
            WHEN SUM(fh_trip_count) > 0 
            THEN ROUND(SUM(fh_fuel_efficiency) / SUM(fh_trip_count), 2) 
        END AS first_half_avg,
        CASE 
            WHEN SUM(sh_trip_count) > 0 
            THEN ROUND(SUM(sh_fuel_efficiency) / SUM(sh_trip_count), 2) 
        END AS second_half_avg
    FROM 
        trip_efficiency 
    GROUP BY 
        driver_id
    HAVING 
        SUM(fh_trip_count) > 0  -- Must have trips in first half
        AND SUM(sh_trip_count) > 0  -- Must have trips in second half
)


SELECT 
    oe.driver_id, 
    d.driver_name,
    oe.first_half_avg,
    oe.second_half_avg,
    (oe.second_half_avg - oe.first_half_avg) AS efficiency_improvement
FROM 
    overall_efficiency oe
JOIN 
    drivers d ON oe.driver_id = d.driver_id
WHERE 
    oe.second_half_avg > oe.first_half_avg  
ORDER BY 
    efficiency_improvement DESC,
    d.driver_name ASC;  

```