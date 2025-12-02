# Find Patients Who Recovered from COVID-19

## Problem Statement

### Table: patients

| Column Name | Type    | Description |
|-------------|---------|-------------|
| patient_id  | int     | Unique identifier for each patient |
| patient_name| varchar | Patient's name |
| age         | int     | Patient's age |

### Table: covid_tests

| Column Name | Type    | Description |
|-------------|---------|-------------|
| test_id     | int     | Unique identifier for each test |
| patient_id  | int     | References patients.patient_id |
| test_date   | date    | Date of the COVID-19 test |
| result      | varchar | Test result: 'Positive', 'Negative', or 'Inconclusive' |

### Key Points:
- A patient is considered recovered if they have at least one positive test followed by at least one negative test on a later date
- Recovery time is calculated as the difference in days between the first positive test and the first subsequent negative test
- Only include patients who have both positive and negative test results
- Order results by recovery time (ascending) and then by patient name (ascending)

## Example

### Input

**patients table:**
| patient_id | patient_name | age |
|------------|--------------|-----|
| 1          | Alice Smith  | 28  |
| 2          | Bob Johnson  | 35  |
| 3          | Carol Davis  | 42  |
| 4          | David Wilson | 31  |
| 5          | Emma Brown   | 29  |

**covid_tests table:**
| test_id | patient_id | test_date  | result       |
|---------|------------|------------|--------------|
| 1       | 1          | 2023-01-15 | Positive     |
| 2       | 1          | 2023-01-25 | Negative     |
| 3       | 2          | 2023-02-01 | Positive     |
| 4       | 2          | 2023-02-05 | Inconclusive |
| 5       | 2          | 2023-02-12 | Negative     |
| 6       | 3          | 2023-01-20 | Negative     |
| 7       | 3          | 2023-02-10 | Positive     |
| 8       | 3          | 2023-02-20 | Negative     |
| 9       | 4          | 2023-01-10 | Positive     |
| 10      | 4          | 2023-01-18 | Positive     |
| 11      | 5          | 2023-02-15 | Negative     |
| 12      | 5          | 2023-02-20 | Negative     |

### Output
| patient_id | patient_name | age | recovery_time |
|------------|--------------|-----|---------------|
| 1          | Alice Smith  | 28  | 10            |
| 3          | Carol Davis  | 42  | 10            |
| 2          | Bob Johnson  | 35  | 11            |

## Solution
-- Get the first positive test date for each patient
-- Find the first negative test after the first positive test
-- Calculate recovery time and join with patient info

```sql
WITH FirstPositiveTest AS (
    
    SELECT 
        patient_id,
        MIN(test_date) AS first_positive_date
    FROM 
        covid_tests
    WHERE 
        result = 'Positive'
    GROUP BY 
        patient_id
),
FirstNegativeAfterPositive AS (
    
    SELECT 
        fp.patient_id,
        fp.first_positive_date,
        MIN(ct.test_date) AS first_negative_date
    FROM 
        FirstPositiveTest fp
    JOIN 
        covid_tests ct ON fp.patient_id = ct.patient_id
    WHERE 
        ct.result = 'Negative'
        AND ct.test_date > fp.first_positive_date
    GROUP BY 
        fp.patient_id, fp.first_positive_date
)

SELECT 
    p.patient_id,
    p.patient_name,
    p.age,
    DATEDIFF(fn.first_negative_date, fn.first_positive_date) AS recovery_time
FROM 
    patients p
JOIN 
    FirstNegativeAfterPositive fn ON p.patient_id = fn.patient_id
ORDER BY 
    recovery_time ASC,
    p.patient_name ASC;
```


