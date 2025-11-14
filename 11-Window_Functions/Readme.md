** Window functions  **

Window functions are a powerful set of SQL functions that perform calculations across a set of table rows that are 
somehow related to the current row, known as the "window."

Unlike aggregate functions (SUM, AVG, COUNT) which collapse the rows into a single summary row (when used with GROUP BY),
a window function retains the individual rows in the result set. It performs the calculation and returns the result for 
that calculation on each individual row.

###### NOTE:
The core advantage of a Window Function is that it allows you to calculate an aggregate or analytical value (like a sum, 
average, or rank) for a defined group of rows (the "window") and then add that calculated value as a new column to every 
original row in the result set, without collapsing those original rows.

Structure of a Window Function 

A window function has a specific syntax that defines the window (the set of rows) over which the calculation is performed:

SQL
```
window_function_name (expression) OVER (
    [PARTITION BY column1, ...]
    [ORDER BY column2, ...]
    [frame_clause]
)

```
Key Components
window_function_name: The function itself (e.g., ROW_NUMBER, RANK, AVG, SUM).

**OVER Clause**: This is the defining keyword that tells SQL to treat the function as a window function.

**PARTITION BY** (Optional): Divides the rows into smaller, non-overlapping groups or "partitions." The function is 
applied independently within each partition. If omitted, the function processes the entire result set as one partition.

**Example**: PARTITION BY Department means the calculation restarts for every new department.

**ORDER BY** (Optional but often necessary): Orders the rows within each partition. This is crucial for ranking functions 
and for cumulative/running calculations.

**frame_clause** (Optional): Defines the subset of rows within the current partition that are considered for the calculation 
(the "window frame"). This is typically used for cumulative or moving averages.

**Examples**: ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW (cumulative sum) or ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING 
(moving average).

Types of Window Functions
Window functions are generally grouped into three categories:

1. Ranking Functions 
These assign a rank or sequential number to each row within a partition, based on the ORDER BY clause.

Function	    Purpose	                                            Example Use
ROW_NUMBER()	Assigns a unique, sequential integer to             Finding the Nth highest value.
                each row.	
RANK()	        Assigns the same rank to ties, but skips the        Ranking products where two products have 
                next rank number.	                                the same score.
                
DENSE_RANK()	Assigns the same rank to ties, but does not         Ranking without gaps in the sequence.
                skip the next rank number.	
NTILE(N)	    Divides the rows into a specified number (N) of     Segmenting customers into quartiles 
                equally sized buckets/groups.	                    (N=4) based on spending.


2. Analytic/Value Functions
These calculate values based on the position of the row within the partition.

Function	        Purpose                                          Example Use
LAG(expr, offset)	Returns the value of expr from a row preceding   Comparing the current month's sales to the previous 
                    the current row by a specified offset.	         month's sales.
LEAD(expr, offset)	Returns the value of expr from a row following   Comparing the current quarter's sales to the next
                    the current row by a specified offset.	         quarter's forecast.
FIRST_VALUE(expr)	Returns the value of expr from the first row in  Comparing every employee's salary to the highest
                    the window frame.                                salary in their department.
                    	


3. Aggregate Window Functions ➕
These are standard aggregate functions (SUM, AVG, COUNT, MIN, MAX) used with the OVER clause. 
They calculate the aggregate value for the defined window but return that value on every row.

Function	Purpose	                                        Example Use
SUM(expr)	Calculates a running total or cumulative sum.	Tracking the total sales year-to-date on a transaction-by-
                                                            transaction basis.
AVG(expr)	Calculates a moving average or average across    Comparing an employee's salary to the average salary
            a partition.	                                  of their department.




**1. Ranking Functions** 
These functions use the ORDER BY clause inside the OVER() to rank the sales within a defined partition (Region).

###### ROW_NUMBER() (Unique Sequence)

Example: Assign a unique number to each sale within its region, ordered by SaleAmount.

SQL
```
SELECT
    *,
    ROW_NUMBER() OVER (
        PARTITION BY Region
        ORDER BY SaleAmount DESC
    ) AS RegionRankNumber
FROM EmployeeSales;
```
```
Region	SaleAmount	RegionRowNumber
East	700	        1
East	700	        2
East	500	        3 

```
###### RANK() (Skips Ranks on Ties)

Example: Rank sales within each region, where tied amounts get the same rank, and the next rank is skipped.

SQL
```
SELECT
    *,
    RANK() OVER (
        PARTITION BY Region
        ORDER BY SaleAmount DESC
    ) AS RegionRank
FROM EmployeeSales;
```

```
Region	SaleAmount	RegionRank
East	700	        1
East	700	        1
East	500	        3 (Skipped Rank 2)
```

###### DENSE_RANK() (No Gaps on Ties)
Example: Rank sales within each region, where tied amounts get the same rank, and the next rank is consecutive (no gaps).

SQL

```
SELECT
    *,
    DENSE_RANK() OVER (
        PARTITION BY Region
        ORDER BY SaleAmount DESC
    ) AS RegionDenseRank
FROM EmployeeSales;
```

```
Region	SaleAmount	RegionDenseRank
East	700	        1
East	700	        1
East	500	        2 (Next rank is 2)
```

**2. Analytic/Value Functions** 

These compare the current row's value to a value from a different row within the partition.

##### LAG() (Previous Row Value)
Example: Compare the current sale amount to the immediate previous sale amount in the same region (ordered by date).

SQL
```
SELECT
    *,
    LAG(SaleAmount, 1) OVER (
        PARTITION BY Region
        ORDER BY SaleDate ASC
    ) AS PreviousSaleAmount
FROM EmployeeSales;
```

```
Region	SaleAmount	SaleDate	PreviousSaleAmount
East	500	        2025-01-01	NULL
East	700	        2025-01-05	500
East	700	        2025-01-15	700
```

##### LEAD() (Next Row Value)
Example: Look ahead to the next sale amount in the same region.

SQL
```
SELECT
    *,
    LEAD(SaleAmount, 1) OVER (
        PARTITION BY Region
        ORDER BY SaleDate ASC
    ) AS NextSaleAmount
FROM EmployeeSales;
```
```
Region	SaleAmount	SaleDate	PreviousSaleAmount
East	500	        2025-01-01	700
East	700	        2025-01-05	700
East	700	        2025-01-15	NULL


```

###### FIRST_VALUE() (Value of First Row)

Example: Find the earliest sale amount for each region (first row after ordering by date).

SQL
```
SELECT
    *,
    FIRST_VALUE(SaleAmount) OVER (
        PARTITION BY Region
        ORDER BY SaleDate ASC
    ) AS EarliestSaleAmount
FROM EmployeeSales;

```


###### LAST_VALUE() 
The LAST_VALUE() function retrieves the value of the specified expression from the last row in the current window frame.

Requires ORDER BY: To define what the "last" row is, you must include an ORDER BY clause in the OVER() part.

Frame Clause is Critical: This is the most common pitfall for LAST_VALUE(). If you omit the frame clause (or rely on the default frame), the function will only consider rows up to the current row. To get the absolute last value of the entire partition, you must explicitly set the frame to include the end of the partition.

Example: Finding the Highest Sale Amount in the Region
Let's use an EmployeeSales table partitioned by Region, ordered by SaleDate.

SQL
```
SELECT
    EmployeeName,
    SaleAmount,
    SaleDate,
    LAST_VALUE(SaleAmount) OVER (
        PARTITION BY Region
        ORDER BY SaleDate ASC
        ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING -- CRITICAL Frame
    ) AS LastSaleInRegion
FROM EmployeeSales;
```

In the example above:

PARTITION BY Region: The calculation is independent for the East and West regions.
ORDER BY SaleDate ASC: Defines the sequence within the partition.
ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING: This is the key. It tells the function, "For the current row, define the window frame to include everything from the current row up to the very end of the partition." This ensures the function can see and grab the value from the last row overall.

###### NTH_VALUE() 
The NTH_VALUE(expression, N) function retrieves the value of the expression from the Nth row in the current window frame, where N is a specified integer.
Requires ORDER BY: You need an ORDER BY clause to consistently define the first, second, third, or Nth row.

Frame Clause is Essential: Like LAST_VALUE(), the frame must be wide enough to include the Nth row you are looking for. Usually, you set the frame from the beginning to the end of the partition.

Example: Finding the Second Highest Sale Amount in the Region
Using the same EmployeeSales table, let's find the value of the sale ranked second highest in each region. To do this, we need to order by SaleAmount DESC (Highest to Lowest).

SQL
```
SELECT
    EmployeeName,
    SaleAmount,
    Region,
    NTH_VALUE(SaleAmount, 2) OVER (
        PARTITION BY Region
        ORDER BY SaleAmount DESC -- Orders from highest sale amount (Rank 1) down
        ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING -- Widest possible frame
    ) AS SecondHighestSale
FROM EmployeeSales;
```


**3. Aggregate Window Functions** 


These use standard aggregates but apply them cumulatively or within a partition while keeping all rows.

##### SUM() (Running Total)
Example: Calculate the running (cumulative) total sales for each employee, ordered by date.

SQL
```
SELECT
    *,
    SUM(SaleAmount) OVER (
        PARTITION BY EmployeeName
        ORDER BY SaleDate ASC
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW -- Defines the cumulative frame
    ) AS EmployeeRunningTotal
FROM EmployeeSales;
```
```
EmployeeName	SaleAmount	SaleDate	EmployeeRunningTotal
Alice	        500	        2025-01-01	500
Bob	            700	        2025-01-05	700
Bob	            700	        2025-01-15	1400 (700+700)
```


SQL
```
SELECT
    *,
    SUM(SaleAmount) OVER (
        PARTITION BY EmployeeName
        ORDER BY SaleDate ASC
        ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING   -- Defines the cumulative frame
    ) AS EmployeeTrailingTotal
FROM EmployeeSales;
```
```
EmployeeName	SaleAmount	SaleDate	EmployeeTrailingTotal
Alice	        500	        2025-01-01	500
Bob	            700	        2025-01-05	1400 (700+700)
Bob	            700	        2025-01-15	700
```

##### AVG() (Partition Average)
Example: Compare the current sale to the average sale amount for its region.

SQL
```
SELECT
    *,
    AVG(SaleAmount) OVER (
        PARTITION BY Region
    ) AS RegionAvgSale
FROM EmployeeSales;
```
This returns the same RegionAvgSale value on every row belonging to that region.



