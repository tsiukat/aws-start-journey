# Lab: Migrating to Amazon RDS

**Course:** AWS Cloud Practitioner Exam Prep  
**Topic:** Database Migration — EC2 Local DB → Amazon RDS MariaDB via AWS CLI  

---

## Overview

Migrated a café web application's local MariaDB database (running on an EC2 LAMP instance) to a fully managed Amazon RDS MariaDB instance — without losing any data.

**Architecture — before and after:**

```
BEFORE:
[Internet] → [CafeInstance EC2 - Public Subnet]
                  └── Apache + PHP + MariaDB (local)

AFTER:
[Internet] → [CafeInstance EC2 - Public Subnet]
                  └── Apache + PHP → (network connection, port 3306)
                                          ↓
                            [RDS MariaDB - Private Subnet 1 / AZ1]
                            [RDS MariaDB - Private Subnet 2 / AZ2]
                            (CafeDB Subnet Group)
```

---

## Task 1 — Generate Order Data Before Migration

Opened café website via `CafeInstanceURL`, placed several orders via the Menu page. Recorded total order count from Order History — used later to verify data integrity after migration.

> **Screenshot:** Café website → Order History showing order count before migration

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/ed5c6f37-ac07-4751-adac-0fc4c4cbd406" />


---

## Task 2 — Create RDS Instance via AWS CLI

Connected to **CLI Host** via EC2 Instance Connect, configured AWS CLI:

```bash
aws configure
# AccessKey / SecretKey / LabRegion / json
```

### Create Security Group for RDS

```bash
aws ec2 create-security-group \
  --group-name CafeDatabaseSG \
  --description "Security group for Cafe database" \
  --vpc-id <CafeVpcID>

# Allow only MySQL (3306) from CafeInstance's security group
aws ec2 authorize-security-group-ingress \
  --group-id <CafeDatabaseSG ID> \
  --protocol tcp --port 3306 \
  --source-group <CafeSecurityGroup ID>
```

### Create Two Private Subnets

```bash
# Private Subnet 1 — same AZ as CafeInstance
aws ec2 create-subnet \
  --vpc-id <CafeVpcID> \
  --cidr-block 10.200.2.0/23 \
  --availability-zone <CafeInstanceAZ>

# Private Subnet 2 — different AZ (e.g. us-west-2b)
aws ec2 create-subnet \
  --vpc-id <CafeVpcID> \
  --cidr-block 10.200.10.0/23 \
  --availability-zone <other-AZ>
```

### Create DB Subnet Group

```bash
aws rds create-db-subnet-group \
  --db-subnet-group-name "CafeDB Subnet Group" \
  --db-subnet-group-description "DB subnet group for Cafe" \
  --subnet-ids <Subnet1ID> <Subnet2ID> \
  --tags "Key=Name,Value=CafeDatabaseSubnetGroup"
```

### Launch RDS MariaDB Instance

```bash
aws rds create-db-instance \
  --db-instance-identifier CafeDBInstance \
  --engine mariadb \
  --engine-version 10.11.11 \
  --db-instance-class db.t3.micro \
  --allocated-storage 20 \
  --availability-zone <CafeInstanceAZ> \
  --db-subnet-group-name "CafeDB Subnet Group" \
  --vpc-security-group-ids <CafeDatabaseSG ID> \
  --no-publicly-accessible \
  --master-username root --master-user-password 'Re:Start!9'
```

Monitored status until **available**:

```bash
aws rds describe-db-instances \
  --db-instance-identifier CafeDBInstance \
  --query "DBInstances[*].[Endpoint.Address,AvailabilityZone,DBInstanceStatus]"
```

> **Screenshot:** Terminal — RDS status = **available** with endpoint address

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/f3d9eee1-5bc5-4930-8a79-bad17c3161c6" />
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/9179795d-cde8-40d2-b7dc-6f880f5b14ca" />


---

## Task 3 — Migrate Data (mysqldump → RDS)

Connected to **CafeInstance** via EC2 Instance Connect.

### Export local database

```bash
mysqldump --user=root --password='Re:Start!9' \
  --databases cafe_db --add-drop-database > cafedb-backup.sql
```

### Download RDS SSL certificate

```bash
curl -o global-bundle.pem https://truststore.pki.rds.amazonaws.com/global/global-bundle.pem
```

### Import backup into RDS

```bash
mysql --user=root --password='Re:Start!9' \
--host=cafedbinstance.ckxhpnoa0kpd.us-west-2.rds.amazonaws.com \
--ssl-ca=./global-bundle.pem \
< cafedb-backup.sql
```

### Verify migration

```bash
mysql --user=root --password='Re:Start!9' \
--host=cafedbinstance.ckxhpnoa0kpd.us-west-2.rds.amazonaws.com \
--ssl-ca=./global-bundle.pem \
-e "use cafe_db; select * from product;"
```

> **Screenshot:** Terminal — `select * from product` showing migrated data in RDS

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/5c7d7a9b-4fba-460e-a2d8-b3450b06b6fc" />


---

## Task 4 — Reconnect Application to RDS

Updated the database connection string in **AWS Systems Manager → Parameter Store**:

| Parameter | Old value | New value |
|---|---|---|
| `/cafe/dbUrl` | localhost | `cafedbinstance.ckxhpnoa0kpd.us-west-2.rds.amazonaws.com` |

No code changes needed — the application reads the endpoint from Parameter Store at runtime.

Verified by opening the café website → Order History showed the **same order count** as before migration. ✅

> **Screenshot:** Café website → Order History — order count matches pre-migration count

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/357ff2e8-4c23-4514-8734-4e2407077172" />


---

## Task 5 — Monitor RDS via CloudWatch

In **RDS Console → cafedbinstance → Monitoring tab**:

Key metrics observed:

| Metric | What it shows |
|---|---|
| `CPUUtilization` | Processing load on the DB instance |
| `DatabaseConnections` | Active connections in real time |
| `FreeStorageSpace` | Available disk space |
| `FreeableMemory` | Available RAM |
| `ReadIOPS` / `WriteIOPS` | Disk I/O per second |

Opened a MySQL session from CafeInstance → `DatabaseConnections` graph showed **1 connection**. Exited session → graph dropped to **0** after 1 minute.

> **Screenshot:** RDS Console → Monitoring tab → DatabaseConnections active session

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/ae18a245-3620-4e87-ac07-7ff871afb28c" />

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/3d3cac7b-c067-40c4-b4df-4d44c7526a68" />

---

## Migration Process — Summary

```
CafeInstance (local MariaDB)
        │
  mysqldump → cafedb-backup.sql
        │
  mysql < cafedb-backup.sql → RDS MariaDB (private subnet)
        │
  Parameter Store: /cafe/dbUrl updated
        │
  Application now reads from RDS ✅
```

---

## Reflection

In this lab I learned how to:

- **Provision a production RDS instance entirely via AWS CLI** — chaining `create-security-group`, `create-subnet`, `create-db-subnet-group`, and `create-db-instance` commands shows how infrastructure can be built programmatically and repeatably
- **Perform a live database migration using `mysqldump`** — export → transfer → import is the standard lift-and-shift pattern for moving databases to managed services, and understanding SSL requirements for RDS connections is a real operational detail
- **Use Parameter Store to decouple configuration from code** — changing the database endpoint in Systems Manager without touching application code is the correct twelve-factor app pattern for externalized configuration
- **Monitor a managed database with CloudWatch** — seeing `DatabaseConnections` respond to a real connection in near-real-time demonstrated that RDS observability is built-in, not an afterthought

> Key takeaway: Migrating from a self-managed database on EC2 to Amazon RDS transfers the operational burden (backups, patching, failover, monitoring) to AWS. The application sees only an endpoint change — everything else improves automatically.

---

*Kateryna | AWS Cloud Practitioner Exam Prep*
