
# Challenge Lab: Build a DB Server and Interact with It via SQL

**Course:** AWS Cloud Practitioner Exam Prep  
**Topic:** Amazon RDS — Independent Deployment + SQL Table Design + JOIN  

---

## Challenge Overview

Independent lab — no step-by-step guidance provided. I had to:
1. Launch an RDS instance from scratch following given constraints
2. Connect via SSH + MySQL client
3. Design and populate two tables
4. Query them individually and with an INNER JOIN

---

## Task 1 — Launch RDS Instance

Deployed a MySQL RDS instance within the following constraints:

| Parameter | Value chosen |
|---|---|
| Engine | MySQL |
| Template | Dev/Test |
| Multi-AZ / Standby | Disabled |
| Instance class | `db.t3.micro` (Burstable) |
| Storage | General Purpose SSD (gp2), up to 100 GB |
| VPC | Lab VPC |
| Security Group | Allowed inbound MySQL (3306) from LinuxServer |
| Enhanced Monitoring | Disabled |

> **Screenshot:** RDS Console → instance status = **Available**
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/a79af504-6ae4-43ab-bbfa-c24164350016" />


---

## Task 2 — Connect to LinuxServer and Install MySQL Client

```bash
chmod 400 labsuser.pem
ssh -i labsuser.pem ec2-user@<LinuxServer-IP>
sudo yum install mysql -y
mysql -u <master-user> --password='<password>' -h <rds-endpoint>
```

> **Screenshot:** Terminal — successful MySQL connection to RDS endpoint

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/8778d9b0-9131-400d-93a4-4b9b081d8878" />


---

## Task 3 — Create and Populate Table: RESTART

```sql
CREATE TABLE RESTART (
  StudentID     INT,
  StudentName   VARCHAR(100),
  RestartCity   VARCHAR(100),
  GraduationDate DATETIME
);
```
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/66ce25fc-174e-4c6f-9002-893fd9259145" />

Inserted 10 sample rows:

```sql
INSERT INTO RESTART (StudentID, StudentName, RestartCity, GraduationDate) VALUES
(1, 'Ivan Petrov', 'Kyiv', '2026-06-15 10:00:00'),
(2, 'Olena Sydorenko', 'Lviv', '2026-06-15 10:00:00'),
(3, 'Andriy Kovalenko', 'Odesa', '2026-06-16 11:30:00'),
(4, 'Mariya Boyko', 'Kharkiv', '2026-06-16 11:30:00'),
(5, 'Dmytro Moroz', 'Dnipro', '2026-06-17 09:00:00'),
(6, 'Anna Melnyk', 'Zaporizhzhia', '2026-06-17 09:00:00'),
(7, 'Sergiy Shevchenko', 'Vinnytsia', '2026-06-18 14:00:00'),
(8, 'Yuliya Kravchenko', 'Poltava', '2026-06-18 14:00:00'),
(9, 'Vitaliy Bondarenko', 'Chernihiv', '2026-06-19 12:00:00'),
(10, 'Tetyana Lysenko', 'Cherkasy', '2026-06-19 12:00:00');
```

> **Screenshot:** `CREATE TABLE RESTART` — Query OK

<img width="1366" height="728" alt="image" src="https://github.com/user-attachments/assets/4d5103fc-c018-405f-a76e-49a7c4214d08" />


> **Screenshot:** `SELECT * FROM RESTART` — all 10 rows

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/1bb0c3fa-6d1b-4a71-804d-286abfce4743" />


---

## Task 4 — Create and Populate Table: CLOUD_PRACTITIONER

```sql
CREATE TABLE CLOUD_PRACTITIONER (
    StudentID INT PRIMARY KEY,
    CertificationDate DATETIME,
    FOREIGN KEY (StudentID) REFERENCES RESTART(StudentID)
);
```

Inserted 5 sample rows:

```sql
INSERT INTO CLOUD_PRACTITIONER VALUES
(1,  '2024-11-01 09:00:00'),
(3,  '2024-11-15 10:00:00'),
(5,  '2024-12-01 09:00:00'),
(8,  '2024-12-10 11:00:00'),
(10, '2025-01-20 09:00:00');
```

> **Screenshot:** `CREATE TABLE CLOUD_PRACTITIONER` — Query OK

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/360e4d87-4fc8-4bc4-8092-487d729a34a3" />


> **Screenshot:** `SELECT * FROM CLOUD_PRACTITIONER` — all 5 rows

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/cfe877f9-ac0a-4bd6-8169-88ca70b67475" />


---

## Task 5 — INNER JOIN: Students with Certifications

```sql
SELECT r.StudentID, r.StudentName, c.CertificationDate
FROM RESTART r
INNER JOIN CLOUD_PRACTITIONER c ON r.StudentID = c.StudentID;
```

Returns only students who appear in **both** tables (5 matched records).

> **Screenshot:** INNER JOIN result — StudentID, StudentName, CertificationDate
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/22586a42-dbb7-4543-b93f-9db22c3ee77d" />


---

## Key Concepts Applied

| Concept | Used in |
|---|---|
| RDS instance provisioning with constraints | Task 1 |
| Security Group scoping (port 3306 to specific source) | Task 1 |
| SSH + MySQL client connection | Task 2 |
| DDL — `CREATE TABLE` with typed columns | Tasks 3–4 |
| DML — `INSERT`, `SELECT` | Tasks 3–4 |
| `INNER JOIN` on shared key | Task 5 |

---

## Reflection

In this challenge lab I learned how to:

- **Work independently within defined infrastructure constraints** — choosing the right instance size, storage type, and network settings without step-by-step prompts, which mirrors real-world cloud deployment decisions
- **Design a relational schema from scratch** — defining column types (`INT`, `VARCHAR`, `DATETIME`) and thinking about which field would serve as the join key between two tables
- **Apply INNER JOIN in a meaningful context** — understanding that only 5 of 10 students appear in `CLOUD_PRACTITIONER`, so the join filters the result set to those who completed both the program and the certification
- **Reinforce the full deployment-to-query workflow**: provision RDS → connect via SSH → create schema → insert data → query results — end to end, independently

> Key takeaway: A challenge lab tests whether I can translate knowledge into decisions. Choosing `db.t3.micro`, scoping the security group to the LinuxServer only, and writing a correct JOIN without guidance confirmed that the previous labs built real transferable skills — not just step-following.

---

*Kateryna | AWS Cloud Practitioner Exam Prep*
