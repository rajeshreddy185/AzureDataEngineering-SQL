UPDATE (Data Modification): Filters which specific rows will have their column values changed.

SQL

`UPDATE Products
SET Price = Price * 1.10
WHERE Category = 'Electronics';` -- Only products in the 'Electronics' category are updated.
