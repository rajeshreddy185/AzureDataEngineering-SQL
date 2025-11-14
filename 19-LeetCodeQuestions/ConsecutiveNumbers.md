Table: Logs

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| num         | varchar |
+-------------+---------+
In SQL, id is the primary key for this table.
id is an autoincrement column starting from 1.

Find all numbers that appear at least three times consecutively.
Return the result table in any order.
The result format is in the following example.

Example 1:

Input: 
Logs table:
+----+-----+
| id | num |
+----+-----+
| 1  | 1   |
| 2  | 1   |
| 3  | 1   |
| 4  | 2   |
| 5  | 1   |
| 6  | 2   |
| 7  | 2   |
+----+-----+


Output: 
+-----------------+
| ConsecutiveNums |
+-----------------+
| 1               |
+-----------------+
Explanation: 1 is the only number that appears consecutively for at least three times.

##### STANDARD SELF JOIN

```commandline

SELECT num FROM  Logs AS l1
JOIN Logs l2 on l1.id=l2.id+1
JOIN Logs l3 on l2.id+1=l3.id+2
WHERE l1.num=l2.num=l3.num;


```

##### WITH LAG

```commandline

WITH ConsecutiveChecks AS (
    SELECT 
        num, 
        LAG(num, 1) OVER (ORDER BY id) AS PrevNum,
        LAG(num, 2) OVER (ORDER BY id) AS PrevtoPrevNum
    FROM 
        Logs
  )

SELECT DISTICT num
FROM ConsecutiveChecks
WHERE num=PrevNum 
    AND PrevNum=PrevtoPrevNum
    AND num IS NOT NULL; 

```


##### WITH LEAD

```commandline

WITH ConsecutiveChecks AS (
    SELECT 
         num,
         LEAD(num, 1) OVER (ORDER BY id) AS NextNum,
         LEAD(num, 2) OVER (ORDER BY id) as NexttoNextNum
    FROM
        Logs
   )

SELECT DISTINCT num
FROM Logs
WHERE num=NextNum
    AND NextNum=NexttoNextNum
    AND NexttoNextNum IS NOT NULL;

```



##### WITH ROW_NUMBER()

```commandline

WITH RowNumLogs AS (
    SELECT num,
            ROW_NUMBER() OVER (ORDER BY id) as r1,
            ROW_NUMBER() OVER (PARTITION BY num, ORDER BY id) as r2
    FROM 
        Logs
)

SELECT DISTINCT num
FROM RowNumLogs
GROUP BY num , (r1 - r2)
HAVING COUNT(*) >= 3;

```

Logs table:
+----+-----+----+-----+
| id | num | id | num |
+----+-----+----+-----+
| 1  | 1   | 1  | 1   |
| 2  | 1   | 2  | 2   |
| 3  | 1   | 3  | 3   |
| 4  | 2   | 4  | 1   |
| 5  | 1   | 5  | 4   |
| 6  | 2   | 6  | 2   |
| 7  | 2   | 7  | 3   |
+----+-----+----+-----+




