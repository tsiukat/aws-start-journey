# Lab: Build a DB Server and Interact with It Using a Web App

**Course:** AWS Cloud Practitioner Exam Prep  
**Topic:** Amazon RDS - Multi-AZ MySQL Instance + Web Application Integration  

---

## Scenario

Deploy a production-grade relational database on Amazon RDS and connect it to a running web application (Address Book). The database must be:
- Highly available (Multi-AZ)
- Accessible only from the web server (not the public internet)
- Hosted in private subnets across two Availability Zones

**Architecture - before and after:**

```
BEFORE:  [User] → [Web Server (EC2)]

AFTER:   [User] → [Web Server (EC2)] → [RDS MySQL - Primary AZ]
                                              ↕ sync replication
                                       [RDS MySQL - Standby AZ]
```

---

## Task 1 - Create Security Group for RDS

In **VPC Console → Security Groups → Create security group**:

| Setting | Value |
|---|---|
| Name | `DB Security Group` |
| VPC | Lab VPC |
| Inbound rule | MySQL/Aurora (port **3306**) from `Web Security Group` |

This restricts database access to the web server only - no public internet access.

> **Screenshot:** DB Security Group - Inbound rules showing port 3306 source

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/f2e75b61-4c0d-4349-a35d-deeb2d8c29b7" />


---

## Task 2 - Create DB Subnet Group

In **RDS Console → Subnet groups → Create DB Subnet Group**:

| Setting | Value |
|---|---|
| Name | `DB Subnet Group` |
| VPC | Lab VPC |
| Subnets | `10.0.1.0/24` (AZ1) + `10.0.3.0/24` (AZ2) |

Two private subnets across two AZs - required for Multi-AZ deployment.

> **Screenshot:** DB Subnet Group created with two subnets

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/87865022-3dd7-4f94-8afd-7e4f23038412" />


---

## Task 3 - Launch Amazon RDS MySQL Instance

In **RDS Console → Create database** with key settings:

| Parameter | Value |
|---|---|
| Engine | MySQL (latest) |
| Template | Dev/Test |
| Availability | **Multi-AZ DB Instance** |
| DB identifier | `lab-db` |
| Master username | `main` |
| Instance class | `db.t3.medium` (Burstable) |
| VPC | Lab VPC |
| Security Group | DB Security Group |
| Initial DB name | `lab` |
| Automated backups | Disabled (lab only) |

> **Screenshot:** RDS Console → `lab-db` status = **Available**
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/d94fc534-9512-43a6-acac-1b2f2339aa59" />


---

## Task 4 - Connect Web App to the Database

Opened the web application via the EC2 public IP → clicked **RDS** tab → entered connection details:

| Field | Value |
|---|---|
| Endpoint | `lab-db.xxxxxxx.us-west-2.rds.amazonaws.com` |
| Database | `lab` |
| Username | `main` |
| Password | `lab-password` |

After submitting, the **Address Book** application loaded - backed by the RDS database. Added, edited, and deleted contacts to confirm data persistence.

> **Screenshot:** Web app - Address Book loaded and connected to RDS

![address-book](screenshots/05-address-book.png)

---

## Architecture Summary

| Layer | Service | Purpose |
|---|---|---|
| Compute | EC2 (Web Server) | Hosts the web application |
| Database | RDS MySQL Multi-AZ | Stores application data with automatic failover |
| Networking | VPC + Private Subnets | Isolates DB from public internet |
| Security | Security Groups | Restricts DB access to web server only |

---

## Reflection

In this lab I learned how to:

- **Deploy a Multi-AZ RDS instance** - understanding that AWS automatically provisions a primary and a standby replica in separate AZs and handles synchronous replication without manual configuration
- **Design network security in layers** - the DB Security Group accepts traffic only from the Web Security Group (not from `0.0.0.0/0`), which is the correct production pattern for protecting a database
- **Create a DB Subnet Group** - grasping why RDS requires subnets in at least two AZs even for a single instance, because Multi-AZ needs room to place the standby
- **Connect an application to a managed database** using an endpoint URL instead of an IP address - endpoints remain stable even after failover, which is a core advantage of managed RDS over self-hosted databases

> Key takeaway: Amazon RDS offloads the hardest parts of database operations - replication, failover, patching - so teams focus on application logic. Multi-AZ is the minimum viable setup for any production workload where downtime is unacceptable.

---

*Kateryna | AWS Cloud Practitioner Exam Prep*
