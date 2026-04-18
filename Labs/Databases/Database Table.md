# Lab — Database Table Operations (SQL)

> **Course:** AWS Cloud Practitioner (CLF-C02)  
> **Topic:** MySQL, SQL DDL, CREATE / SHOW / ALTER / DROP  


---

## Lab Objective

Connect to a MySQL database instance and practice core Data Definition Language (DDL) operations: create a database and tables, inspect and fix a schema error, and drop tables and databases.

---

## SQL Commands Covered

| Command | Purpose |
|---------|---------|
| `CREATE DATABASE` | Create a new database |
| `CREATE TABLE` | Define a table with columns and data types |
| `SHOW DATABASES` | List all available databases |
| `SHOW TABLES` | List tables in the current database |
| `SHOW COLUMNS FROM` | Display table schema — column names, types, constraints |
| `ALTER TABLE ... RENAME COLUMN` | Fix a column name without recreating the table |
| `DROP TABLE` | Permanently delete a table |
| `DROP DATABASE` | Permanently delete a database and all its tables |

---

## Tasks Completed

### ✅ Task 1 — Connected to the Database Instance
- Connected to the `Command Host` EC2 instance via **Session Manager**
- Logged into MySQL: `mysql -u root --password='re:St@rt!9'`

### ✅ Task 2 — Created a Database and Table

Created database and `country` table in the `world` database:

```sql
CREATE DATABASE world;

CREATE TABLE world.country (
  `Code` CHAR(3) NOT NULL,
  `Name` CHAR(52) NOT NULL,
  `Conitinent` enum('Asia','Europe','North America',...),
  `Region` CHAR(26) NOT NULL,
  `Population` INT(11) NOT NULL,
  PRIMARY KEY (`Code`)
);
```

Discovered a typo: column `Conitinent` → fixed with ALTER:

```sql
ALTER TABLE world.country RENAME COLUMN Conitinent TO Continent;
```

**Challenge 1 — Created a `city` table with two CHAR columns:**

```sql
CREATE TABLE world.city (
  `Name` CHAR(52) NOT NULL DEFAULT '',
  `Region` CHAR(26) NOT NULL DEFAULT ''
);
```

### ✅ Task 3 — Dropped Tables and Database

**Challenge 2 — Dropped the `country` table:**

```sql
DROP TABLE world.country;
```

Then dropped the `city` table and the entire `world` database:

```sql
DROP TABLE world.city;
DROP DATABASE world;
```

Verified with `SHOW DATABASES` — `world` no longer listed.

---

## Screenshots

### SHOW DATABASES — Before and After CREATE

<!-- Add screenshot: terminal showing world database appears after CREATE DATABASE -->
![Show Databases](screenshots/task2-show-databases.png)

---

### SHOW COLUMNS — Typo Found (Conitinent)

<!-- Add screenshot: SHOW COLUMNS output with misspelled "Conitinent" visible -->
![Typo Found](screenshots/task2-typo-column.png)

---

### ALTER TABLE — Column Renamed

<!-- Add screenshot: SHOW COLUMNS after ALTER showing corrected "Continent" -->
![Column Fixed](screenshots/task2-column-fixed.png)

---

### DROP DATABASE — world Removed

<!-- Add screenshot: SHOW DATABASES after DROP DATABASE showing world is gone -->
![DB Dropped](screenshots/task3-db-dropped.png)

---

## Key Takeaways

| Concept | What I Learned |
|---------|----------------|
| **DDL vs DML** | DDL (CREATE, ALTER, DROP) defines structure; DML (INSERT, SELECT, UPDATE) manipulates data |
| **Table Schema** | A table must have a defined structure before any data can be stored — column names, types, and constraints |
| **ALTER TABLE** | Modifies an existing table structure without data loss — safer than dropping and recreating |
| **DROP is permanent** | Dropped tables and databases cannot be recovered without a backup — always verify before running |
| **PRIMARY KEY** | Uniquely identifies each row — `Code` in the `country` table ensures no duplicate entries |
| **Data types** | `CHAR` for fixed-length strings, `INT` for integers, `FLOAT` for decimals, `ENUM` for predefined values |

---

## My Reflection

This lab was a shift from AWS services to the fundamentals underneath them — the SQL that powers relational databases like Amazon RDS and Aurora. Working directly in the MySQL CLI made the connection between "creating a database" and the actual commands that make it happen very concrete.

The typo in the `Conitinent` column name was a small but important teaching moment. In a real database with existing data, you cannot just delete and recreate the column — you would lose everything in it. `ALTER TABLE ... RENAME COLUMN` is the safe path: it modifies the structure while leaving the data intact.

The irreversibility of `DROP` was the most sobering detail. There is no confirmation prompt, no undo, no recycle bin. Running `DROP TABLE world.country` deletes the table instantly and permanently. That is why in production environments, DROP commands require backups, approval workflows, and maintenance windows.

> 💡 **Key Insight:** A database schema is a contract. Every column name, data type, and constraint is a design decision that affects every query, every application, and every future migration. Getting it right at the start — and knowing how to safely change it later — is foundational database engineering.

---

*Author: Kateryna | AWS Cloud Practitioner Exam Prep*
