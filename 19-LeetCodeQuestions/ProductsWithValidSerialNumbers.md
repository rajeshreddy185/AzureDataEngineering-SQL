# Products with Valid Serial Numbers

## Problem Description

### Table: Products

| Column Name  | Type    | Description |
|--------------|---------|-------------|
| product_id   | int     | Unique identifier |
| product_name | varchar | Name of the product |
| description  | varchar | Product description (may contain serial numbers) |

**Note:** `product_id` is the primary key for this table.

## Task

Find all products whose description contains a valid serial number pattern. A valid serial number must:
1. Start with the letters `SN` (case-sensitive)
2. Be followed by exactly 4 digits
3. Have a hyphen `-`
4. End with exactly 4 more digits
5. The serial number can appear anywhere in the description

Return the result ordered by `product_id` in ascending order.

## Example

### Input:

**Products table:**
| product_id | product_name | description |
|------------|--------------|------------------------------------------------------|
| 1          | Widget A     | This is a sample product with SN1234-5678            |
| 2          | Widget B     | A product with serial SN9876-1234 in the description |
| 3          | Widget C     | Product SN1234-56789 is available now                |
| 4          | Widget D     | No serial number here                                |
| 5          | Widget E     | Check out SN4321-8765 in this description            |

### Output:
| product_id | product_name | description |
|------------|--------------|------------------------------------------------------|
| 1          | Widget A     | This is a sample product with SN1234-5678            |
| 2          | Widget B     | A product with serial SN9876-1234 in the description |
| 5          | Widget E     | Check out SN4321-8765 in this description            |

### Explanation:
- Product 1: Valid serial number `SN1234-5678`
- Product 2: Valid serial number `SN9876-1234`
- Product 3: Invalid - has 5 digits after the hyphen
- Product 4: No serial number
- Product 5: Valid serial number `SN4321-8765`

## Solution:

### Using REGEXP (MySQL/PostgreSQL):
```sql
SELECT 
    product_id,
    product_name,
    description
FROM 
    Products
WHERE 
    description REGEXP 'SN[0-9]{4}-[0-9]{4}'
    AND description REGEXP 'SN[0-9]{4}-[0-9]{4}([^0-9]|$)'
ORDER BY 
    product_id;
```

### Using REGEXP with word boundaries (PostgreSQL, some MySQL versions):
```sql
SELECT 
    product_id,
    product_name,
    description
FROM 
    Products
WHERE 
    description ~ '\mSN\d{4}-\d{4}\M'
ORDER BY 
    product_id;
```

### Using LIKE with additional checks (More portable):
```sql
SELECT 
    product_id,
    product_name,
    description
FROM 
    Products
WHERE 
    description LIKE '%SN____-____%'  -- Basic pattern matching
    AND (
        -- Check the format more precisely
        description REGEXP 'SN[0-9]{4}-[0-9]{4}'
        AND NOT description REGEXP 'SN[0-9]{5,}-'  -- No more than 4 digits before -
        AND NOT description REGEXP 'SN[0-9]*-[0-9]{5,}'  -- No more than 4 digits after -
    )
ORDER BY 
    product_id;
```

### Notes:
- The first solution uses two REGEXP checks to ensure the exact pattern is matched
- The second solution uses word boundaries (`\m` and `\M`) for more precise matching
- The third solution combines LIKE with REGEXP for better portability across SQL dialects
- All solutions ensure the serial number has exactly 4 digits before and after the hyphen
- The pattern `[0-9]` can be replaced with `\d` in most SQL dialects
