# Market Analysis I

## Problem Statement

### Table: Users

| Column Name    | Type    | Description |
|----------------|---------|-------------|
| user_id        | int     | Primary Key |
| join_date      | date    | User's join date |
| favorite_brand | varchar | User's favorite brand |

order_id is the primary key (column with unique values) of this table.
item_id is a foreign key (reference column) to the Items table.
buyer_id and seller_id are foreign keys to the Users table.
### Table: Orders

| Column Name | Type | Description |
|-------------|------|-------------|
| order_id    | int  | Primary Key |
| order_date  | date | Order date  |
| item_id     | int  | Foreign Key to Items |
| buyer_id    | int  | Foreign Key to Users (buyer) |
| seller_id   | int  | Foreign Key to Users (seller) |

### Table: Items
item_id is the primary key (column with unique values) of this table.
 

Write a solution to find for each user, the join date and the number of orders they made as a buyer in 2019.

Return the result table in any order.



| Column Name | Type    | Description |
|-------------|---------|-------------|
| item_id     | int     | Primary Key |
| item_brand  | varchar | Brand name  |

## Example

### Input

**Users:**
| user_id | join_date  | favorite_brand |
|---------|------------|----------------|
| 1       | 2018-01-01 | Lenovo         |
| 2       | 2018-02-09 | Samsung        |
| 3       | 2018-01-19 | LG             |
| 4       | 2018-05-21 | HP             |

**Orders:**
| order_id | order_date | item_id | buyer_id | seller_id |
|----------|------------|---------|----------|-----------|
| 1        | 2019-08-01 | 4       | 1        | 2         |
| 2        | 2018-08-02 | 2       | 1        | 3         |
| 3        | 2019-08-03 | 3       | 2        | 3         |
| 4        | 2018-08-04 | 1       | 4        | 2         |
| 5        | 2018-08-04 | 1       | 3        | 4         |
| 6        | 2019-08-05 | 2       | 2        | 4         |

**Items:**
| item_id | item_brand |
|---------|------------|
| 1       | Samsung    |
| 2       | Lenovo     |
| 3       | LG         |
| 4       | HP         |

The result format is in the following example.
### Output
| buyer_id | join_date  | orders_in_2019 |
|----------|------------|----------------|
| 1        | 2018-01-01 | 1              |
| 2        | 2018-02-09 | 2              |
| 3        | 2018-01-19 | 0              |
| 4        | 2018-05-21 | 0              |

```sql
SELECT 
    u.user_id AS buyer_id,
    u.join_date,
    COUNT(CASE WHEN YEAR(o.order_date) = 2019 THEN o.order_id END) AS orders_in_2019
FROM 
    Users u
LEFT JOIN 
    Orders o ON u.user_id = o.buyer_id 
    AND YEAR(o.order_date) = 2019
GROUP BY 
    u.user_id, 
    u.join_date
ORDER BY 
    u.user_id;

```