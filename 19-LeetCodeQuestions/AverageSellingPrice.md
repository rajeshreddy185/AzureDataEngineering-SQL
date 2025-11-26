Table: Prices

| Column Name   | Type    |
|---------------|---------|
| product_id    | int     |
| start_date    | date    |
| end_date      | date    |
| price         | int     |

(product_id, start_date, end_date) is the primary key (combination of columns with unique values) for this table.
Each row of this table indicates the price of the product_id in the period from start_date to end_date.
For each product_id there will be no two overlapping periods. That means there will be no two intersecting periods for the same product_id.
 

Table: UnitsSold

| Column Name   | Type    |
|---------------|---------|
| product_id    | int     |
| purchase_date | date    |
| units         | int     |
| PRIMARY KEY (product_id, purchase_date)

This table may contain duplicate rows.
Each row of this table indicates the date, units, and product_id of each product sold. 
 

Write a solution to find the average selling price for each product. average_price should be rounded to 2 decimal places. If a product does not have any sold units, its average selling price is assumed to be 0.

Return the result table in any order.

The result format is in the following example.

 

Example 1:

Input: 
Prices table:

| product_id | start_date | end_date   | price  |
|------------|------------|------------|--------|
| 1          | 2019-02-17 | 2019-02-28 | 5.00   |
| 1          | 2019-03-01 | 2019-03-22 | 20.00  |
| 2          | 2019-02-01 | 2019-02-20 | 15.00  |
| 2          | 2019-02-21 | 2019-03-31 | 30.00  |
UnitsSold table:

| product_id | purchase_date | units |
|------------|---------------|-------|
| 1          | 2019-02-25    | 100   |
| 1          | 2019-03-01    | 15    |
| 2          | 2019-02-10    | 200   |
| 2          | 2019-03-22    | 30    |
Output: 

| product_id | average_price |
|------------|---------------|
| 1          | 6.96          |
| 2          | 16.96         |

Explanation: 
Average selling price = Total Price of Product / Number of products sold.
Average selling price for product 1 = ((100 * 5) + (15 * 20)) / 115 = 6.96
Average selling price for product 2 = ((200 * 15) + (30 * 30)) / 230 = 16.96

```sql

SELECT
    P.product_id,
    ROUND(
        CASE 
            WHEN SUM(US.units) > 0 
            THEN SUM(P.price * US.units) * 1.0 / SUM(US.units)
            ELSE 0 
        END, 
    2) AS average_price
FROM
    Prices AS P
LEFT JOIN
    UnitsSold AS US
    ON P.product_id = US.product_id
    AND US.purchase_date BETWEEN P.start_date AND P.end_date
GROUP BY
    P.product_id;
```

```sql

WITH ProductRevenue AS (
    -- Step 1: Match every unit sold to the correct price based on the purchase date range
    SELECT
        US.product_id,
        -- Calculate the revenue for each transaction (Units * Price)
        (US.units * P.price) AS revenue,
        US.units
    FROM
        UnitsSold AS US
    INNER JOIN
        Prices AS P
        ON US.product_id = P.product_id
        -- The purchase date must fall within the price's active date range
        AND US.purchase_date BETWEEN P.start_date AND P.end_date
),

AggregatedPrice AS (
    -- Step 2: Calculate the total revenue and total units sold for each product
    SELECT
        product_id,
        SUM(revenue) AS total_revenue,
        SUM(units) AS total_units
    FROM
        ProductRevenue
    GROUP BY
        product_id
)

-- Step 3: Select all products and calculate the final average price.

SELECT
    P.product_id,
    ROUND(
        -- Calculate (Total Revenue / Total Units). 
        -- COALESCE handles products with zero sales (where the join result is NULL), setting the price to 0.
        COALESCE(A.total_revenue / A.total_units, 0),
    2) AS average_price
FROM
    (SELECT DISTINCT product_id FROM Prices) AS P -- Anchor: Get all product IDs defined in the Prices table
LEFT JOIN
    AggregatedPrice AS A ON P.product_id = A.product_id;

```
 

