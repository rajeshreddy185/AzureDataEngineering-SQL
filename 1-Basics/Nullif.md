** NULLIF **

##### NULLIF() (Replacing Specific Values)
The NULLIF function is used to compare two expressions. If the two expressions are equal, it returns NULL; otherwise, 
it returns the value of the first expression.

Syntax:

SQL

NULLIF(expression1, expression2)
Purpose: To turn a specific, meaningful value (like a placeholder or error code) into a standard NULL. 
This is often necessary when calculating averages or sums, as aggregate functions ignore NULLs but 
include zeros or placeholder text.

How it Works:
```
If expression1 = expression2, the result is NULL.

If expression1 <> expression2, the result is expression1.
```

Example
Suppose a Rating column uses 0 as a placeholder for "Not Applicable" ratings, and you don't want those zeros to skew 
your average rating calculation.

SQL

```
SELECT
    AVG(NULLIF(Rating, 0)) AS ActualAverageRating
FROM
    ProductRatings;

```
In the AVG calculation, the NULL value is ignored, but the 0 would have been counted, 
thus improving the accuracy of the average.