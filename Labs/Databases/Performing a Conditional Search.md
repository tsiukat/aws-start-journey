# Lab: Performing a Conditional Search in SQL

**Course:** AWS Cloud Practitioner Exam Prep  
**Topic:** Querying a Relational Database — WHERE, BETWEEN, LIKE, Functions  

---


## Key Queries

### WHERE + AND — filter by population range

```sql
SELECT Name, Capital, Region, SurfaceArea, Population
FROM world.country
WHERE Population >= 50000000 AND Population <= 100000000;
```

### BETWEEN — cleaner range syntax (inclusive)

```sql
SELECT Name, Capital, Region, SurfaceArea, Population
FROM world.country
WHERE Population BETWEEN 50000000 AND 100000000;
```

> **Screenshot:** Query result — countries with population between 50M and 100M

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/66633c69-87ac-4ab6-8483-9044503bec60" />


---

### LIKE + SUM — aggregate with pattern matching

```sql
SELECT sum(Population) as "Europe Population Total"
FROM world.country
WHERE Region LIKE "%Europe%";
```

> **Screenshot:** Query result showing Europe Population Total

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/388e082e-35f2-4d35-80f6-b5983e6ecfaf" />


---

### LOWER() — case-insensitive search

```sql
SELECT Name, Capital, Region, SurfaceArea, Population
FROM world.country
WHERE LOWER(Region) LIKE "%central%";
```
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/3fbbd032-f167-4aa1-8d4b-ed49dd0c9168" />

---

### Challenge — North America surface area + population totals

```sql
SELECT sum(SurfaceArea) as "NA Surface Area", sum(Population) as "NA Population"
FROM world.country
WHERE Region LIKE "%North America%";
```

> **Screenshot:** Challenge query result
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/a61241f3-dfe0-46b0-b1bd-1a4048917df0" />

---

## SQL Operators & Functions — Quick Reference

| Tool | Purpose |
|---|---|
| `WHERE` | Filter rows by condition |
| `AND` / `OR` | Combine multiple conditions |
| `BETWEEN x AND y` | Inclusive range filter |
| `LIKE "%pattern%"` | Pattern matching with wildcard |
| `SUM()` | Aggregate sum of a column |
| `AS` | Rename column in output (alias) |
| `LOWER()` | Convert to lowercase for case-safe comparison |

---

## Reflection

In this lab I learned how to:

- **Write conditional SQL queries** using `WHERE` with comparison operators (`>=`, `<=`, `AND`) and understand how each layer of filtering narrows the result set
- **Use `BETWEEN` as a cleaner alternative** to paired comparison operators — both return identical results, but `BETWEEN` improves readability
- **Apply `LIKE` with `%` wildcards** to search for partial string matches across column values — useful when the exact value is unknown
- **Aggregate data with `SUM()`** combined with pattern-based filtering to produce summary statistics from a subset of records
- **Handle case sensitivity** with `LOWER()` — a practical technique for real-world databases where collation settings may vary

> Key takeaway: SQL filtering logic maps directly to how AWS services query and filter data (e.g., CloudWatch Logs Insights, Athena). Understanding `WHERE`, `LIKE`, and aggregation functions is foundational for both database work and cloud data analysis.

---

*Kateryna | AWS Cloud Practitioner Exam Prep*
