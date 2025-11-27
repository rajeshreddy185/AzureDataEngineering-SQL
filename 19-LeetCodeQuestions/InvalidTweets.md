# Invalid Tweets

## Problem Description

### Table: Tweets

| Column Name | Type    |
|-------------|---------|
| tweet_id    | int     |
| content     | varchar |

- `tweet_id` is the primary key (column with unique values) for this table.
- `content` contains only alphanumeric characters, spaces, and exclamation marks.

## Task

Find the IDs of tweets that are invalid. A tweet is invalid if its content has more than 15 characters.

Return the result in any order.

## Example

### Input:

**Tweets table:**
| tweet_id | content                           |
|----------|-----------------------------------|
| 1        | Let us Code                       |
| 2        | More than fifteen chars are here! |

### Output:
| tweet_id |
|----------|
| 2        |

### Explanation:
- Tweet 1 has 11 characters (valid)
- Tweet 2 has 33 characters (invalid, as it's > 15)

### Solution:

```sql
SELECT 
    tweet_id
FROM 
    Tweets
WHERE 
    LENGTH(content) > 15;