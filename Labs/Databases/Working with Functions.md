# Lab: Working with SQL Functions

**Course:** AWS Cloud Practitioner Exam Prep  
**Topic:** Database Functions — Aggregate, String, and Filter Functions  


---

## Setup

Connected to EC2 (Command Host) via **Session Manager**, then logged into MySQL:

```bash
sudo su
cd /home/ec2-user/
mysql -u root --password='re:St@rt!9'
```

Database in use: `world` — tables: `city`, `country`, `countrylanguage`.

> **Screenshot:** Terminal showing successful MySQL login prompt

![mysql-connected](screenshots/01-mysql-connected.png)

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

![aggregate-result](screenshots/02-aggregate-result.png)

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

![substring-result](screenshots/03-substring-result.png)

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

![distinct-result](screenshots/04-distinct-result.png)

---

### Challenge — Split "Micronesian/Caribbean" into two columns

```sql
SELECT
  substring_index(Region, "/", 1) AS "Region Name 1",
  substring_index(Region, "/", -1) AS "Region Name 2"
FROM world.country
WHERE Region = "Micronesian/Caribbean";
```

> **Screenshot:** Challenge query result — two separate region columns

![challenge-result](screenshots/05-challenge-result.png)

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
