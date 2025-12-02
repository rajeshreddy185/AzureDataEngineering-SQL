# Tree Node

## Problem Description

### Table: Tree

| Column Name | Type | Description |
|-------------|------|-------------|
| id          | int  | Unique identifier for each node |
| p_id        | int  | Parent node's ID (NULL for root) |

**Notes:**
- `id` is the primary key
- Each row represents a node in a tree
- The structure is always a valid tree

## Task

Determine the type of each node in the tree:
- "Root": Node with no parent (p_id is NULL)
- "Leaf": Node with no children and has a parent
- "Inner": Node with both parent and at least one child

Return the result in any order.

## Examples

### Example 1:

**Input:**
```
+----+------+
| id | p_id |
+----+------+
| 1  | null |
| 2  | 1    |
| 3  | 1    |
| 4  | 2    |
| 5  | 2    |
+----+------+
```

**Output:**
```
+----+-------+
| id | type  |
+----+-------+
| 1  | Root  |
| 2  | Inner |
| 3  | Leaf  |
| 4  | Leaf  |
| 5  | Leaf  |
+----+-------+
```

**Explanation:**
- Node 1 is the root (no parent)
- Node 2 is inner (parent: 1, children: 4,5)
- Nodes 3,4,5 are leaves (have parents, no children)

### Example 2:

**Input:**
```
+----+------+
| id | p_id |
+----+------+
| 1  | null |
+----+------+
```

**Output:**
```
+----+------+
| id | type |
+----+------+
| 1  | Root |
+----+------+
```

## Solution:

```sql

-- Root nodes (no parent)
SELECT 
    id,
    'Root' AS type
FROM
    Tree 
WHERE 
    p_id IS NULL

UNION ALL

-- Inner nodes (have both parent and at least one child)
SELECT 
    t1.id, 
    'Inner' AS type
FROM
    Tree t1
JOIN 
    Tree t2 ON t1.id = t2.p_id
WHERE 
    t1.p_id IS NOT NULL
GROUP BY 
    t1.id

UNION ALL

-- Leaf nodes (have parent but no children)
SELECT 
    id,
    'Leaf' AS type
FROM 
    Tree
WHERE 
    id NOT IN (SELECT DISTINCT p_id FROM Tree WHERE p_id IS NOT NULL)
    AND p_id IS NOT NULL
ORDER BY 
    id;
    
```

### Using CASE with EXISTS:

```sql
SELECT 
    id,
    CASE 
        WHEN p_id IS NULL THEN 'Root'
        WHEN EXISTS (
            SELECT 1 
            FROM Tree t2 
            WHERE t2.p_id = t1.id
        ) THEN 'Inner'
        ELSE 'Leaf'
    END AS type
FROM 
    Tree t1
ORDER BY 
    id;
```

### Using LEFT JOIN and GROUP BY:
```sql
SELECT 
    t1.id,
    CASE 
        WHEN t1.p_id IS NULL THEN 'Root'
        WHEN t2.id IS NULL THEN 'Leaf'
        ELSE 'Inner'
    END AS type
FROM 
    Tree t1
LEFT JOIN 
    Tree t2 ON t1.id = t2.p_id
GROUP BY 
    t1.id, t1.p_id
ORDER BY 
    t1.id;
```

### Using CASE with IN and NOT IN:
```sql
SELECT 
    id,
    CASE 
        WHEN p_id IS NULL THEN 'Root'
        WHEN id IN (SELECT DISTINCT p_id FROM Tree WHERE p_id IS NOT NULL) THEN 'Inner'
        ELSE 'Leaf'
    END AS type
FROM 
    Tree
ORDER BY 
    id;
```

### Notes:
1. The first solution uses EXISTS to check for child nodes
2. The second solution uses a self-join to identify parent-child relationships
3. The third solution uses IN to find nodes that are parents of other nodes
4. All solutions correctly identify Root, Inner, and Leaf nodes
5. Results are ordered by id for consistent output