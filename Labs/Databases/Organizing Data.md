# Lab: Organizing Data with GROUP BY and Window Functions

**Course:** AWS Cloud Practitioner Exam Prep  
**Topic:** SQL Data Grouping — GROUP BY, OVER, RANK()  


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

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/f84c13f9-e510-456d-893e-6b47adc94eb2" />


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

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/6719dae6-d2cd-4732-981b-d86dcd8b1d56" />




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

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/4b51e55d-7a11-4e63-84f0-4bf64d38e710" />


---

### Challenge — Rank all countries in every region by population (largest to smallest)

```sql
SELECT Region, Name, Population,
  RANK() OVER(PARTITION BY Region ORDER BY Population DESC) AS 'Rank'
FROM world.country;
```

> **Screenshot:** Challenge query result — all regions with country ranks
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/0d97d1af-4757-468b-8934-2c9f64a6cf2d" />
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/323d08f1-6f56-44c1-a582-c42f16eefbbb" />
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/de44a058-32f2-41bc-bafa-5ce9846d56bd" />




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
