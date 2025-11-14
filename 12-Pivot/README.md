** PIVOT and UNPIVOT **

PIVOT and UNPIVOT are relational operators in SQL used to change a table's structure by rotating column values into row 
values and vice-versa. This is often necessary for data presentation (pivoting) or data analysis/loading (unpivoting).

##### PIVOT
The PIVOT operation takes unique values from one column (the spreading column) and transforms them into multiple new
columns. The values that populate these new columns are usually derived from an aggregate function (like SUM or COUNT).

Goal: To rotate data from a long, vertical format (many rows) into a wide, horizontal format (many columns).

Analogy: Turning a list of transactions into a monthly summary report.

Conceptual Example
Imagine a table of sales where each row is a monthly sale for a specific product:

Product	Month	SalesAmount
TV	    Jan	    1000
TV	    Feb	    1200
Radio	Jan	    500

Export to Sheets
Pivot Goal: Group by Product and use the unique values from the Month column (Jan, Feb) to create new columns, populating them with the aggregated SalesAmount.

Product	Jan	    Feb
TV	    1000	1200
Radio	500	    NULL

##### UNPIVOT

The UNPIVOT operation is the reverse of PIVOT. It rotates columns into rows, reducing the number of columns and 
increasing the number of rows.

Goal: To transform data from a wide, horizontal format (many columns) into a long, vertical format (few columns).

Analogy: Transforming a static report back into a transactional format for analysis.

Conceptual Example
Starting with the pivoted table above:

Product	Jan	    Feb
TV	    1000	1200

Unpivot Goal: Collapse the Jan and Feb columns back into two columns: one column for the name of the original column (Month) and one for the value of that column (SalesAmount).

Product	Month	SalesAmount
TV	    Jan	    1000
TV	    Feb	    1200



