# Lab: Introduction to Amazon Aurora

**Course:** AWS Cloud Practitioner Exam Prep  
**Topic:** Amazon Aurora — Managed Relational Database  

---

## What is Amazon Aurora?

Amazon Aurora is a fully managed, MySQL-compatible relational database engine built on Amazon RDS. It delivers up to **5× the performance of MySQL** while handling administration tasks automatically — backups, patching, failover.

Key services used in this lab:
- **Amazon Aurora** — managed relational DB engine
- **Amazon RDS** — platform for provisioning and managing Aurora
- **Amazon EC2** — virtual server used as a client to connect to the database

---

## Task 1 — Create an Aurora DB Instance

Configured via **RDS → Create database** with the following key settings:

| Parameter | Value |
|---|---|
| Engine | Aurora (MySQL Compatible) |
| Template | Dev/Test |
| DB cluster identifier | `aurora` |
| Instance class | `db.t3.medium` (Burstable) |
| Multi-AZ | Disabled |
| VPC | LabVPC |
| Security Group | DBSecurityGroup |
| Initial database name | `world` |

> **Screenshot:** RDS Console → Databases → `aurora` status = **Available**

![aurora-instance-available](screenshots/01-aurora-instance-available.png)

---

## Task 2 — Connect to EC2 (Command Host)

Connected to the pre-created EC2 instance `Command Host` via **Session Manager** (no SSH key needed).

> **Screenshot:** EC2 Console → Instances → Connect → Session Manager terminal open

![session-manager-connected](screenshots/02-session-manager-connected.png)

---

## Task 3 — Connect EC2 to Aurora

**Install MariaDB client:**
```bash
sudo yum install mariadb -y
```

**Connect to Aurora using the cluster Writer endpoint:**
```bash
mysql -u admin --password='admin123' -h <writer-endpoint>
```

The Writer endpoint follows the pattern:
```
aurora.cluster-xxxxxxxx.us-west-2.rds.amazonaws.com
```

> **Screenshot:** Terminal showing successful MariaDB connection → `MySQL [(none)]>` prompt

![mysql-connected](screenshots/03-mysql-connected.png)

---

## Task 4 — Create Table, Insert Data, Query

**Switch to the `world` database and verify:**
```sql
SHOW DATABASES;
USE world;
```

**Create the `country` table:**
```sql
CREATE TABLE `country` (
  `Code` CHAR(3) NOT NULL,
  `Name` CHAR(52) NOT NULL,
  `Continent` ENUM('Asia','Europe','North America','Africa','Oceania','Antarctica','South America'),
  `Population` INT(11) NOT NULL,
  `GNP` FLOAT(10,2),
  PRIMARY KEY (`Code`)
);
```

**Insert 5 records** (Gabon, Ireland, Thailand, Costa Rica, Australia), then run a filtered query:

```sql
SELECT * FROM country WHERE GNP > 35000 AND Population > 10000000;
```

**Expected result:** 2 rows — `Australia` and `Thailand`

> **Screenshot:** Query result showing AUS and THA rows in the terminal

![query-result](screenshots/04-query-result.png)

---

## Aurora Endpoint Types — Key Concept

| Endpoint | Purpose |
|---|---|
| **Writer (Cluster)** | Read + Write operations, DDL statements |
| **Reader** | Read-only, load-balanced across replicas |

In this lab, only the Writer endpoint was used since no Aurora Replica was created.

---

## Reflection

In this lab I learned how to:

- **Provision a managed relational database** on AWS using Amazon RDS/Aurora — understanding that Aurora handles backups, patching, and failover automatically, unlike self-managed databases
- **Understand the difference between cluster endpoint types** — Writer vs. Reader — and when to use each in production architectures
- **Connect a compute resource (EC2) to a database (Aurora)** using security groups and VPC networking, rather than public internet access
- **Run SQL queries** against a cloud-hosted MySQL-compatible database from a remote client

> Key takeaway: Aurora abstracts infrastructure complexity (Multi-AZ, replication, failover) so developers focus on data, not administration — which directly maps to the AWS Cloud value proposition of *managed services over self-managed resources*.

---

*Kateryna | AWS Cloud Practitioner Exam Prep*
