# Lab: Introduction to Amazon DynamoDB

**Course:** AWS Cloud Practitioner Exam Prep  
**Topic:** NoSQL Database — DynamoDB Table, Items, Query vs Scan  

---

## What is Amazon DynamoDB?

DynamoDB is a fully managed **NoSQL** database with single-digit millisecond latency at any scale. Unlike relational databases, it uses a flexible schema — each item (row) can have different attributes (columns).

| Concept | DynamoDB | Relational DB equivalent |
|---|---|---|
| Table | Table | Table |
| Item | Item | Row |
| Attribute | Attribute | Column |
| Partition Key | Required unique identifier | Primary Key |
| Sort Key | Optional secondary key | Composite Key |

---

## Task 1 — Create a Table

In **DynamoDB Console → Create table**:

| Setting | Value |
|---|---|
| Table name | `Music` |
| Partition key | `Artist` (String) |
| Sort key | `Song` (String) |

> **Screenshot:** DynamoDB Console → `Music` table status = **Active**

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/38ebe576-905b-4384-802d-a3f4ae967055" />


---

## Task 2 — Add Items

Added 3 items with **different attributes per item** — demonstrating NoSQL schema flexibility:

| Artist | Song | Album | Year | Extra attribute |
|---|---|---|---|---|
| Pink Floyd | Money | The Dark Side of the Moon | 1973 | — |
| John Lennon | Imagine | Imagine | 1971 | `Genre: Soft rock` |
| Psy | Gangnam Style | Psy 6 (Six Rules), Part 1 | 2012 | `LengthSeconds: 219` |

Each item can have unique attributes — no need to pre-define a fixed schema.

> **Screenshot:** DynamoDB → Explore Items — all 3 items visible in the table

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/e38651bc-a5a6-4906-b7a0-80e2ec5df7d9" />


---

## Task 3 — Query vs Scan

### Query — fast, uses primary key index

```
Partition key: Psy
Sort key: Gangnam Style
```

> **Screenshot:** Query result — Psy / Gangnam Style returned
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/db361118-a67f-4294-8819-a0ac0606ce55" />

Returns the exact item instantly via index lookup.

### Scan + Filter — slower, reads entire table

```
Attribute: Year
Type: Number
Value: 1971
```
> **Screenshot:** Scan + Filter result — John Lennon (Year = 1971)
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/6db8e42f-4a20-4f09-b023-5098394116ab" />

Scans all items, then filters — less efficient for large tables.



---

## Query vs Scan — Key Difference

| Operation | How it works | Performance |
|---|---|---|
| **Query** | Uses partition key index directly | Fast — O(1) lookup |
| **Scan** | Reads every item in the table | Slow — O(n), avoid on large tables |

---

## Reflection

In this lab I learned how to:

- **Understand the NoSQL data model** — DynamoDB does not enforce a fixed schema, so items in the same table can have completely different attributes; this is the fundamental difference from relational databases like MySQL or Aurora
- **Design a DynamoDB key structure** — the combination of Partition Key + Sort Key uniquely identifies each item and determines how data is distributed and accessed across servers
- **Distinguish Query from Scan** — Query is the correct production pattern because it uses the index; Scan reads the entire table and should be avoided at scale
- **Experience schema flexibility in practice** — adding `Genre` to one item and `LengthSeconds` to another without altering the table structure illustrates why NoSQL fits unstructured or variable data well

> Key takeaway: DynamoDB is the AWS answer to applications that need massive scale, low latency, and flexible data shapes — use cases like gaming leaderboards, IoT event streams, and session stores. For structured relational data, RDS/Aurora remains the right choice.

---

*Kateryna | AWS Cloud Practitioner Exam Prep*
