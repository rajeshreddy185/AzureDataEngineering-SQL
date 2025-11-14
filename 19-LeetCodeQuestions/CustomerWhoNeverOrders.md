# Customers Who Never Order

## Problem Statement
Write a solution to find all customers who never order anything.

### Customers Table

| Column Name | Type    | Description |
|-------------|---------|-------------|
| id          | int     | Primary key |
| name        | varchar | Customer name|

### Orders Table

| Column Name | Type    | Description |
|-------------|---------|-------------|
| id          | int     | Primary key |
| customerId  | int     | Foreign key referencing Customers(id) |

## Example

### Input

**Customers Table:**
| id | name  |
|----|-------|
| 1  | Joe   |
| 2  | Henry |
| 3  | Sam   |
| 4  | Max   |

**Orders Table:**
| id | customerId |
|----|------------|
| 1  | 3          |
| 2  | 1          |

### Expected Output
| Customers |
|-----------|
| Henry     |
| Max       |

## Solution

### Using LEFT JOIN (Recommended)
```sql
SELECT c.name AS Customers
FROM Customers c
LEFT JOIN Orders o ON c.id = o.customerId
WHERE o.id IS NULL;
```

### Using NOT IN
```sql
SELECT name AS Customers
FROM Customers
WHERE id NOT IN (
    SELECT customerId 
    FROM Orders
    WHERE customerId IS NOT NULL
);
```

### Using NOT EXISTS
```sql
SELECT name AS Customers
FROM Customers c
WHERE NOT EXISTS (
    SELECT 1 
    FROM Orders o 
    WHERE o.customerId = c.id
);
```

### Explanation
1. **LEFT JOIN approach**: Joins the Customers table with the Orders table and filters for customers with no matching orders (where o.id IS NULL).
2. **NOT IN approach**: Selects customers whose IDs don't appear in the list of customer IDs who have placed orders.
3. **NOT EXISTS approach**: Uses a correlated subquery to find customers for whom no corresponding order exists.

The LEFT JOIN approach is generally the most efficient for this type of query in most database systems.