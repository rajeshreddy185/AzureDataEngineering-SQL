# Daily Sales Analysis

## Problem Description

### Table: DailySales

| Column Name | Type    |
|-------------|---------|
| date_id     | date    |
| make_name   | varchar |
| lead_id     | int     |
| partner_id  | int     |

- There is no primary key; the table may contain duplicates.
- `make_name` contains only lowercase English letters.
- Each row represents a sale with date, product name, lead ID, and partner ID.

## Task

For each combination of `date_id` and `make_name`, calculate:
1. The number of distinct `lead_id`s
2. The number of distinct `partner_id`s

Return the result in any order.

## Example

### Input:

**DailySales table:**

| date_id    | make_name | lead_id | partner_id |
|------------|-----------|---------|------------|
| 2020-12-08 | toyota    | 0       | 1          |
| 2020-12-08 | toyota    | 1       | 0          |
| 2020-12-08 | toyota    | 1       | 2          |
| 2020-12-07 | toyota    | 0       | 2          |
| 2020-12-07 | toyota    | 0       | 1          |
| 2020-12-08 | honda     | 1       | 2          |
| 2020-12-08 | honda     | 2       | 1          |
| 2020-12-07 | honda     | 0       | 1          |
| 2020-12-07 | honda     | 1       | 2          |
| 2020-12-07 | honda     | 2       | 1          |

### Output:
| date_id    | make_name | unique_leads | unique_partners |
|------------|-----------|--------------|-----------------|
| 2020-12-08 | toyota    | 2            | 3               |
| 2020-12-07 | toyota    | 1            | 2               |
| 2020-12-08 | honda     | 2            | 2               |
| 2020-12-07 | honda     | 3            | 2               |

### Solution:

```sql
SELECT 
    date_id,
    make_name,
    COUNT(DISTINCT lead_id) AS unique_leads,
    COUNT(DISTINCT partner_id) AS unique_partners
FROM 
    DailySales
GROUP BY 
    date_id, make_name
ORDER BY 
    date_id DESC, make_name;