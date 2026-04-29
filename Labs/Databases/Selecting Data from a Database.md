# Lab  — Selecting Data from a Database (SQL Queries)

> **Course:** AWS Cloud Practitioner (CLF-C02)  
> **Topic:** MySQL, SELECT, WHERE, ORDER BY, COUNT, Comparison Operators  

---

## Lab Objective

Query the pre-created `world` database using SELECT statements with filters, ordering, column aliases, and aggregate functions — building from simple reads to multi-condition queries.

---

## Query Progression

```sql
-- 1. All rows and columns
SELECT * FROM world.country;

-- 2. Count rows
SELECT COUNT(*) FROM world.country;

-- 3. Specific columns with alias
SELECT Name, Capital, Region, SurfaceArea AS "Surface Area", Population
FROM world.country;

-- 4. Ordered by population (ascending → descending)
SELECT Name, Capital, Region, SurfaceArea AS "Surface Area", Population
FROM world.country ORDER BY Population DESC;

-- 5. Filter: population > 50 million
SELECT Name, Capital, Region, SurfaceArea AS "Surface Area", Population
FROM world.country
WHERE Population > 50000000
ORDER BY Population DESC;

-- 6. Multi-condition: population between 50M and 100M
SELECT Name, Capital, Region, SurfaceArea AS "Surface Area", Population
FROM world.country
WHERE Population > 50000000 AND Population < 100000000
ORDER BY Population DESC;
```

**Challenge — Which country in Southern Europe has population > 50,000,000?**

```sql
SELECT Name, Region, Population
FROM world.country
WHERE Region = 'Southern Europe' AND Population > 50000000;
```

---

## Screenshots

### Full Table Query — SELECT *

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/6ca27084-0e99-46b0-9a74-0dc8967cd03d" />

---

### COUNT() — Number of Rows

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/10c97040-ab4a-44e2-a47a-f2acb5f329c9" />


---

### ORDER BY Population DESC with WHERE Filter

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/d290c8d7-0319-4b08-8525-0a3946e1e538" />

---

### Challenge Result — Southern Europe > 50M

SELECT Name, Region, Population
FROM world.country
WHERE Region = 'Southern Europe' AND Population > 50000000;

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/1dfe5ae0-5056-4eef-bce5-7b02bbb88e47" />

---

## Key Takeaways

| Concept | What I Learned |
|---------|----------------|
| **SELECT** | The core read operation — `*` returns all columns; listing names returns only what you need |
| **COUNT(\*)** | Counts all rows regardless of NULL values — useful for quick data validation |
| **Column Alias (`AS`)** | Renames a column in the output — does not change the schema, just the display label |
| **ORDER BY** | Sorts result set — ascending by default; add `DESC` for descending |
| **WHERE** | Filters which rows are returned — the most important clause for precise data retrieval |
| **AND operator** | Combines multiple conditions — all conditions must be true for a row to be included |
| **Comparison operators** | `>`, `<`, `=` work on numbers and strings — foundational for any WHERE clause |

---

## My Reflection

This lab felt like the point where SQL clicked as a tool rather than a syntax exercise. Writing the query progression — from `SELECT *` to a filtered, ordered, multi-condition query — showed how each clause adds a layer of precision to the same basic operation.

The `AS` alias was a small but useful detail: data in databases is often stored with technical column names that mean nothing to a business user. Renaming `SurfaceArea` to `"Surface Area"` in the output is exactly the kind of thing that makes a query result presentable without touching the underlying schema.

The Challenge query was the most satisfying part — combining `Region = 'Southern Europe'` with `Population > 50000000` required thinking about what the data actually contains, not just applying a template. That is what SQL queries feel like in practice: translating a business question into a precise filter.

> 💡 **Key Insight:** A SELECT statement is not just about reading data — it is about asking a precise question and getting a precise answer. WHERE, ORDER BY, and AND are the vocabulary for that precision. Every report, dashboard, and data export in AWS starts with a query like these.

---

*Author: Kateryna | AWS Cloud Practitioner Exam Prep*
