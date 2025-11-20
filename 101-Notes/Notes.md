
1.  the MAX() function cannot be used in the HAVING clause directly on the result of COUNT(*).

2. NULL Handling: the subquery returns an empty set, and MAX() over an empty set returns NULL.