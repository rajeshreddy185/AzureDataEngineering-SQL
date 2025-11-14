**Combine Two Tables**

### Person Table

| Column Name | Type    | Description |
|-------------|---------|-------------|
| personId    | int     | Primary key |
| lastName    | varchar | Person's last name |
| firstName   | varchar | Person's first name |

### Address Table

| Column Name | Type    | Description |
|-------------|---------|-------------|
| addressId   | int     | Primary key |
| personId    | int     | Foreign key to Person table |
| city        | varchar | City name |
| state       | varchar | State name |

## Problem Statement
Write a solution to report the first name, last name, city, and state of each person in the Person table. If the address of a personId is not present in the Address table, report null instead.

### Example Input

**Person Table:**
| personId | lastName | firstName |
|----------|----------|-----------|
| 1        | Wang     | Allen     |
| 2        | Alice    | Bob       |

**Address Table:**
| addressId | personId | city          | state      |
|-----------|----------|---------------|------------|
| 1         | 2        | New York City | New York   |
| 2         | 3        | Leetcode      | California |

### Expected Output
| firstName | lastName | city          | state    |
|-----------|----------|---------------|----------|
| Allen     | Wang     | null          | null     |
| Bob       | Alice    | New York City | New York |

## Solution
```sql
SELECT p.firstName, p.lastName, a.city, a.state
FROM Person p
LEFT JOIN Address a ON p.personId = a.personId;