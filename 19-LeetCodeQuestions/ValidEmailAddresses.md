# Valid Email Addresses

## Problem Description

### Table: Users

| Column Name | Type    | Description |
|-------------|---------|-------------|
| user_id     | int     | Unique identifier |
| email       | varchar | Email address |

**Note:** `user_id` is the primary key for this table.

## Task

Find all valid email addresses that meet the following criteria:
1. Contains exactly one `@` symbol
2. Ends with `.com`
3. The part before `@` contains only alphanumeric characters and underscores
4. The part between `@` and `.com` contains only letters

Return the result ordered by `user_id` in ascending order.

## Example

### Input:

**Users table:**
| user_id | email               |
|---------|---------------------|
| 1       | alice@example.com   |
| 2       | bob_at_example.com  |
| 3       | charlie@example.net |
| 4       | david@domain.com    |
| 5       | eve@invalid         |

### Output:
| user_id | email             |
|---------|-------------------|
| 1       | alice@example.com |
| 4       | david@domain.com  |

### Explanation:
- `alice@example.com` is valid (meets all criteria)
- `bob_at_example.com` is invalid (no @ symbol)
- `charlie@example.net` is invalid (doesn't end with .com)
- `david@domain.com` is valid (meets all criteria)
- `eve@invalid` is invalid (doesn't end with .com)

## Solution:

### Using REGEXP (MySQL/PostgreSQL):
```sql
SELECT 
    user_id,
    email
FROM 
    Users
WHERE 
    email REGEXP '^[A-Za-z0-9_]+@[A-Za-z]+\.com$'
ORDER BY 
    user_id;
```

### Using LIKE with additional checks (More portable across SQL dialects):
```sql
SELECT 
    user_id,
    email
FROM 
    Users
WHERE 
    email LIKE '%@%.com' 
    AND email NOT LIKE '%@%@%'  -- Exactly one @
    AND email REGEXP '^[A-Za-z0-9_]+@[A-Za-z]+\.com$'  -- Validate characters
    AND LENGTH(SUBSTRING_INDEX(email, '@', 1)) > 0  -- Has content before @
    AND LENGTH(SUBSTRING_INDEX(SUBSTRING_INDEX(email, '@', -1), '.', 1)) > 0  -- Has domain name
ORDER BY 
    user_id;
```

### Using SUBSTRING_INDEX and REGEXP (Alternative approach):
```sql
SELECT 
    user_id,
    email
FROM 
    Users
WHERE 
    email LIKE '%@%.com'  -- Must contain @ and end with .com
    AND SUBSTRING_INDEX(email, '@', 1) REGEXP '^[A-Za-z0-9_]+$'  -- Only alphanumeric and _ before @
    AND SUBSTRING_INDEX(SUBSTRING_INDEX(email, '@', -1), '.', 1) REGEXP '^[A-Za-z]+$'  -- Only letters between @ and .com
    AND email = CONCAT(
        SUBSTRING_INDEX(email, '@', 1),
        '@',
        SUBSTRING_INDEX(SUBSTRING_INDEX(email, '@', -1), '.', 1),
        '.com'
    )  -- Ensure no extra characters after .com
ORDER BY 
    user_id;
```

### Notes:
- The first solution using REGEXP is the most concise and readable
- The second solution provides better compatibility across different SQL dialects
- The third solution demonstrates an alternative approach using string functions
- All solutions ensure the email ends with .com and has valid characters in both local and domain parts
