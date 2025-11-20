Table: World

| name        | varchar(255) |
| continent   | varchar(255) |
| area        | int          |
| population  | int          |
| gdp         | bigint       |

name is the primary key (column with unique values) for this table.
Each row of this table gives information about the name of a country, the continent to which it belongs, its area, the population, and its GDP value.
 

A country is big if:

it has an area of at least three million (i.e., 3000000 km2), or
it has a population of at least twenty-five million (i.e., 25000000).
Write a solution to find the name, population, and area of the big countries.

Return the result table in any order.

The result format is in the following example.

 

Example 1:

Input: 
World table:

| name        | continent | area    | population | gdp          |
|-------------|-----------|---------|------------|--------------|
| Afghanistan | Asia      | 652230  | 25500100   | 20343000000  |
| Albania    | Europe     | 28748   | 2831741    | 12960000000  |
| Algeria    | Africa     | 2381741 | 37100000   | 188681000000 |
| Andorra    | Europe     | 468     | 78115      | 3712000000   |
| Angola     | Africa     | 1246700 | 20609294   | 100990000000 |
Output: 

| name        | population | area    |
|-------------|------------|---------|
| Afghanistan | 2,550,0100 | 652,230 |
| Algeria     | 3,710,0000 | 2,381,741 |


```sql

SELECT 
    name,
    population,
    area
FROM 
    World
WHERE 
    area >= 3000000 
    OR population >= 25000000;
    
    
```
