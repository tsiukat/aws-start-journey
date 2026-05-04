# Lab: Working with SQL Functions

**Course:** AWS Cloud Practitioner Exam Prep  
**Topic:** Database Functions — Aggregate, String, and Filter Functions  


---

## Key Queries

### Aggregate Functions — summarize entire table

```sql
SELECT sum(Population), avg(Population), max(Population), min(Population), count(Population)
FROM world.country;
```

| Function | What it does |
|---|---|
| `SUM()` | Total of all values |
| `AVG()` | Mean across all rows |
| `MAX()` | Highest value |
| `MIN()` | Lowest value |
| `COUNT()` | Number of non-null rows |

> **Screenshot:** Query result showing all five aggregate values in one row

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/4d6be3f7-15cf-4d27-9c2e-d741ec7cc7cb" />


---

### SUBSTRING_INDEX() — split a string at a delimiter

```sql
-- Split Region name at the first space
SELECT Region, substring_index(Region, " ", 1)
FROM world.country;

-- Use split result as a filter condition
SELECT Name, Region
FROM world.country
WHERE substring_index(Region, " ", 1) = "Southern";
```

> **Screenshot:** Query result — countries where Region starts with "Southern"

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/b627660e-8a59-48be-9486-d9411a84cec5" />



---

### LENGTH() + TRIM() + DISTINCT() — filter and deduplicate

```sql
-- Regions with fewer than 10 characters (with duplicates)
SELECT Region FROM world.country
WHERE LENGTH(TRIM(Region)) < 10;

-- Same query, duplicates removed
SELECT DISTINCT(Region) FROM world.country
WHERE LENGTH(TRIM(Region)) < 10;
```

> **Screenshot:** DISTINCT result — unique short region names only

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/33a366dd-802d-4522-9879-13578bcbef90" />
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/22c6ed09-2253-4769-beb8-2b6c4adf4289" />


---

### Challenge — Split "Micronesian/Caribbean" into two columns

```sql
SELECT
  substring_index(Region, "/", 1) AS "Region Name 1",
  substring_index(Region, "/", -1) AS "Region Name 2"
FROM world.country
WHERE Region = "Micronesia/Caribbean";
```

> **Screenshot:** Challenge query result — two separate region columns

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/e2bb7bc6-f66a-4398-b2f7-1b04155db7fb" />


---

## Functions Quick Reference

| Function | Purpose |
|---|---|
| `SUM()` / `AVG()` / `MAX()` / `MIN()` / `COUNT()` | Aggregate numeric data |
| `SUBSTRING_INDEX(str, delim, n)` | Extract part of a string before/after delimiter |
| `LENGTH(str)` | Count characters in a string |
| `TRIM(str)` | Remove leading/trailing whitespace |
| `DISTINCT()` | Remove duplicate values from result set |

---

## Reflection

In this lab I learned how to:

- **Use aggregate functions** (`SUM`, `AVG`, `MAX`, `MIN`, `COUNT`) to compute summary statistics from an entire table in a single query — a foundation for any data analysis or reporting task
- **Manipulate strings with `SUBSTRING_INDEX()`** to extract parts of values based on a delimiter, and use the result directly inside a `WHERE` clause as a filter condition
- **Combine `LENGTH()` and `TRIM()`** to measure string length accurately by stripping invisible whitespace before counting characters
- **Eliminate duplicates with `DISTINCT()`** — understanding that raw query results often contain repetition that needs to be collapsed for meaningful output

> Key takeaway: SQL functions transform raw data into usable information without changing the underlying table. This pattern — query → filter → aggregate → alias — is the same logic used in AWS analytics services like Athena and CloudWatch Logs Insights.

---

*Kateryna | AWS Cloud Practitioner Exam Prep*
