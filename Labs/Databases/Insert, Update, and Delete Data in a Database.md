# Lab Insert, Update, and Delete Data in a Database

> **Course:** AWS Cloud Practitioner (CLF-C02)  
> **Topic:** MySQL, SQL DML, INSERT / UPDATE / DELETE / Import  

---


## Lab Objective

Practice core Data Manipulation Language (DML) operations on a pre-created `world` database: insert rows, update values, delete records, and bulk-import data from an SQL backup file.

---

## SQL Commands Covered

| Command | Purpose |
|---------|---------|
| `INSERT INTO ... VALUES` | Add new rows to a table |
| `UPDATE ... SET` | Modify values in existing rows |
| `DELETE FROM` | Remove rows from a table |
| `SELECT * FROM` | Read and verify table contents |
| `mysql < file.sql` | Bulk import data from an SQL script file |

---

## Tasks Completed

### ✅ Task 1 — Connected to the Database
- Connected to `Command Host` EC2 via **Session Manager**
- Logged into MySQL: `mysql -u root --password='re:St@rt!9'`
- Confirmed `world` database exists with `SHOW DATABASES`

### ✅ Task 2 — Inserted Data

```sql
INSERT INTO world.country VALUES
('IRL','Ireland','Europe','British Islands',70273.00,1921,3775100,76.8,75921.00,73132.00,'Ireland/Éire','Republic',1447,'IE');

INSERT INTO world.country VALUES
('AUS','Australia','Oceania','Australia and New Zealand',7741220.00,1901,18886000,79.8,351182.00,392911.00,'Australia','Constitutional Monarchy, Federation',135,'AU');
```
<img width="1920" height="1040" alt="Imported Data from SQL File" src="https://github.com/user-attachments/assets/0a1d8d3e-dc48-42e7-8da2-b57fd750cb00" />

Verified with `SELECT * FROM world.country WHERE Code IN ('IRL', 'AUS')`

### ✅ Task 3 — Updated Rows

```sql
-- Update all rows (no WHERE = affects every row)
UPDATE world.country SET Population = 0;

-- Update multiple columns
UPDATE world.country SET Population = 100, SurfaceArea = 100;
```
<img width="1920" height="1040" alt="Updated Rows" src="https://github.com/user-attachments/assets/008fc53e-e66e-45eb-bcaf-d9633d0a18c9" />

> ⚠️ UPDATE without WHERE clause affects **all rows** in the table

### ✅ Task 4 — Deleted Rows

```sql
SET FOREIGN_KEY_CHECKS = 0;
DELETE FROM world.country;
```
<img width="1920" height="1040" alt="Deleted Rows" src="https://github.com/user-attachments/assets/dd98ef51-aa0a-4434-a6ab-a859e1f68c49" />

> ⚠️ DELETE without WHERE clause **permanently removes all rows** — no undo

### ✅ Task 5 — Imported Data from SQL File

Exited MySQL, then ran bulk import from a pre-downloaded SQL script:

```bash
mysql -u root --password='re:St@rt!9' < /home/ec2-user/world.sql
```

- Restored all three tables: `city`, `country`, `countrylanguage`
- Verified with `SHOW TABLES` and `SELECT * FROM country`
<img width="1920" height="1040" alt="Imported Data from SQL File" src="https://github.com/user-attachments/assets/594f9545-a880-44bf-a0fe-a403d2ef2e3c" />

---

## Key Takeaways

| Concept | What I Learned |
|---------|----------------|
| **DML vs DDL** | DML (INSERT, UPDATE, DELETE) manipulates data; DDL (CREATE, ALTER, DROP) changes structure |
| **INSERT column order** | Values in INSERT must match the exact column order defined in the table schema |
| **UPDATE without WHERE** | Modifies every row in the table — always use WHERE unless a full-table update is intentional |
| **DELETE without WHERE** | Removes every row — just as dangerous as DROP but leaves the table structure intact |
| **FOREIGN_KEY_CHECKS = 0** | Temporarily disables referential integrity checks — needed when deleting rows that other tables reference |
| **SQL bulk import** | Piping a `.sql` file directly to MySQL (`mysql < file.sql`) is the standard way to restore or seed a database |

---

## My Reflection

This lab was the practical counterpart to the previous one — where Lab 16 defined the structure, Lab 17 filled it with data and showed what happens when that data is changed or removed.

The most important lesson was the **danger of missing WHERE clauses**. Both UPDATE and DELETE worked silently and instantly on every row — no warning, no confirmation prompt. In a production database with thousands of rows, an accidental `UPDATE world.country SET Population = 0` without a WHERE condition would be a serious incident. That is why transactions, backups, and code reviews exist.

The bulk import via `mysql < world.sql` was also practically valuable. In real environments, databases are seeded, migrated, and restored using SQL scripts — not row-by-row INSERT statements. Understanding that pattern connects directly to how Amazon RDS snapshots and database migrations work in AWS.

> 💡 **Key Insight:** DML is powerful and immediate — there is no undo. The WHERE clause is not just a filter, it is a safety boundary. Every INSERT, UPDATE, and DELETE should be reviewed as carefully as a destructive infrastructure change.

---

*Author: Kateryna | AWS Cloud Practitioner Exam Prep*
