# Lab: Organizing Data with GROUP BY and Window Functions

**Course:** AWS Cloud Practitioner Exam Prep  
**Topic:** SQL Data Grouping — GROUP BY, OVER, RANK()  
**Duration:** ~45 min

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

### GROUP BY + SUM() — aggregate by region

```sql
SELECT Region, SUM(Population)
FROM world.country
WHERE Region = 'Australia and New Zealand'
GROUP BY Region
ORDER BY SUM(Population) DESC;
```

Returns one row per region with the total population.

> **Screenshot:** Query result — one row with Region + total Population

![groupby-result](screenshots/02-groupby-result.png)

---

### OVER() + SUM() — running total (window function)

```sql
SELECT Region, Name, Population,
  SUM(Population) OVER(PARTITION BY Region ORDER BY Population) AS 'Running Total'
FROM world.country
WHERE Region = 'Australia and New Zealand';
```

Unlike `GROUP BY`, `OVER()` keeps individual rows visible while adding a cumulative total column alongside each record.

> **Screenshot:** Query result showing Population + Running Total per country

![running-total](screenshots/03-running-total.png)

---

### OVER() + RANK() — rank rows within a group

```sql
SELECT Region, Name, Population,
  SUM(Population) OVER(PARTITION BY Region ORDER BY Population) AS 'Running Total',
  RANK() OVER(PARTITION BY Region ORDER BY Population) AS 'Ranked'
FROM world.country
WHERE Region = 'Australia and New Zealand';
```

`RANK()` assigns a position number to each row within its partition, ordered by Population.

> **Screenshot:** Query result with Running Total + Ranked columns

![rank-result](screenshots/04-rank-result.png)

---

### Challenge — Rank all countries in every region by population (largest to smallest)

```sql
SELECT Region, Name, Population,
  RANK() OVER(PARTITION BY Region ORDER BY Population DESC) AS 'Rank'
FROM world.country;
```

> **Screenshot:** Challenge query result — all regions with country ranks

![challenge-result](screenshots/05-challenge-result.png)

---

## GROUP BY vs OVER — Key Difference

| Clause | Rows in output | Use when |
|---|---|---|
| `GROUP BY` | Collapsed — one row per group | You only need the aggregate value |
| `OVER()` | All rows preserved | You need both the detail and the aggregate side by side |

---

## Reflection

In this lab I learned how to:

- **Use `GROUP BY` with `SUM()`** to collapse multiple rows into a single aggregate result per group — the standard approach for reporting totals by category
- **Understand window functions with `OVER()`** — the key insight is that `PARTITION BY` groups rows logically without collapsing them, so each row retains its own data while gaining an aggregate column
- **Build a running total** using `SUM() OVER(PARTITION BY ... ORDER BY ...)` — a pattern widely used in financial and time-series analysis
- **Apply `RANK()`** to assign ordered positions within each partition, which is useful for leaderboards, top-N analysis, and competitive comparisons across large datasets

> Key takeaway: Window functions (`OVER`) are significantly more powerful than `GROUP BY` for analytical queries because they preserve row-level detail. This distinction matters in AWS data services like Amazon Redshift and Athena, which both support window functions for large-scale analytics.

---

*Kateryna | AWS Cloud Practitioner Exam Prep*
