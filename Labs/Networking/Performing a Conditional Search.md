# Lab: Performing a Conditional Search in SQL

**Course:** AWS Cloud Practitioner Exam Prep  
**Topic:** Querying a Relational Database — WHERE, BETWEEN, LIKE, Functions  

---

## Setup

Connected to EC2 (Command Host) via **Session Manager**, then logged into MySQL:

```bash
sudo su
cd /home/ec2-user/
mysql -u root --password='re:St@rt!9'
```

Database in use: `world` — contains tables `city`, `country`, `countrylanguage`.

> **Screenshot:** Terminal showing successful MySQL login prompt

![mysql-connected](screenshots/01-mysql-connected.png)

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

![between-result](screenshots/02-between-result.png)

---

### LIKE + SUM — aggregate with pattern matching

```sql
SELECT sum(Population) as "Europe Population Total"
FROM world.country
WHERE Region LIKE "%Europe%";
```

> **Screenshot:** Query result showing Europe Population Total

![sum-europe](screenshots/03-sum-europe.png)

---

### LOWER() — case-insensitive search

```sql
SELECT Name, Capital, Region, SurfaceArea, Population
FROM world.country
WHERE LOWER(Region) LIKE "%central%";
```

---

### Challenge — North America surface area + population totals

```sql
SELECT sum(SurfaceArea) as "NA Surface Area", sum(Population) as "NA Population"
FROM world.country
WHERE Region LIKE "%North America%";
```

> **Screenshot:** Challenge query result

![challenge-result](screenshots/04-challenge-result.png)

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
