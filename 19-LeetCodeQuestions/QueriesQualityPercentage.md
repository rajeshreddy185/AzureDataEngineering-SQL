## 📝 Queries Table Definition

| Column Name | Type    |
|-------------|---------|
| query_name  | varchar |
| result      | varchar |
| position    | int     |
| rating      | int     |

**Notes:**
* The table may contain duplicate rows.
* `position` values range from 1 to 500.
* `rating` values range from 1 to 5.
* A query with a rating less than 3 is considered a **poor query**.

---

## 💡 Definitions

### Query Quality

The average of the ratio between query rating and its position:
$$\text{Quality} = \text{Average}\left( \frac{\text{rating}}{\text{position}} \right)$$

### Poor Query Percentage

The percentage of all queries with a rating less than 3:
$$\text{Poor Query Percentage} = \frac{\text{Count of queries with rating} < 3}{\text{Total Count of queries}} \times 100$$

---

## 🎯 Task

Write a solution to find each **`query_name`**, its calculated **`quality`**, and its **`poor_query_percentage`**.

* Both results should be **rounded to 2 decimal places**.
* The result table can be returned in any order.

---

## 📋 Example 1

**Input: `Queries` table**

| query_name | result | position | rating |
|------------|-------------------|----------|--------|
| Dog | Golden Retriever | 1 | 5 |
| Dog | German Shepherd | 2 | 5 |
| Dog | Mule | 200 | 1 |
| Cat | Shirazi | 5 | 2 |
| Cat | Siamese | 3 | 3 |
| Cat | Sphynx | 7 | 4 |

**Output**

| query_name | quality | poor_query_percentage |
|------------|---------|-----------------------|
| Dog | 2.50 | 33.33 |
| Cat | 0.66 | 33.33 |

**Explanation:**

* **Dog queries quality:** $((5 / 1) + (5 / 2) + (1 / 200)) / 3 = 2.50$
* **Dog poor query percentage:** $(1 / 3) \times 100 = 33.33$
* **Cat queries quality:** $((2 / 5) + (3 / 3) + (4 / 7)) / 3 \approx 0.66$
* **Cat poor query percentage:** $(1 / 3) \times 100 = 33.33$



```sql

SELECT 
    query_name, 
    ROUND(SUM(rating/position)/COUNT(*), 2) as quality,
    ROUND(SUM(CASE WHEN rating < 3 THEN 1 ELSE 0) * 100 / COUNT(*), 2) AS poor_query_percentage 
FROM 
    Queries
GROUP BY
    query_name

```