# Lab: Optimize Utilization — EC2 Right-Sizing + AWS Pricing Calculator

**Course:** AWS Cloud Practitioner Exam Prep  
**Topic:** EC2 Cost Optimization — Instance Resize, Storage Cleanup, Pricing Calculator  

---

## Business Context

After migrating the café database to Amazon RDS (previous lab), the local MariaDB on the EC2 instance was no longer needed. This created two optimization opportunities:

1. **Uninstall the decommissioned local database** → reduce storage from 40 GB to 20 GB
2. **Downsize instance type** from `t3.small` to `t3.micro` → CPU/memory no longer needed for DB process

**Architecture — before and after:**

```
BEFORE:
CafeInstance (t3.small)          RDS MariaDB (db.t3.micro)
  ├── Apache + PHP                  └── cafe_db (migrated)
  ├── MariaDB (decommissioned ❌)
  └── EBS: 40 GB (20 GB wasted)

AFTER:
CafeInstance (t3.micro) ✅       RDS MariaDB (db.t3.micro)
  ├── Apache + PHP                  └── cafe_db
  └── EBS: 20 GB ✅
```

---

## Task 1 — Optimize the EC2 Instance via AWS CLI

Connected to **CafeInstance** and **CLI Host** via separate SSH sessions.

### Step 1: Uninstall local MariaDB (on CafeInstance)

```bash
sudo systemctl stop mariadb
sudo yum -y remove mariadb-server
# Expected: Complete!
```
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/d101c9e2-5ac2-4bcb-bd29-8d1c8d2d2f20" />


### Step 2: Get CafeInstance ID (on CLI Host)

```bash
aws ec2 describe-instances \
  --filters "Name=tag:Name,Values=CafeInstance" \
  --query "Reservations[*].Instances[*].InstanceId"
```

### Step 3: Stop → Resize → Start (on CLI Host)

```bash
# Stop the instance
aws ec2 stop-instances --instance-ids <InstanceId>

# Change instance type (instance must be stopped first)
aws ec2 modify-instance-attribute \
  --instance-id <InstanceId> \
  --instance-type "{\"Value\": \"t3.micro\"}"

# Start the instance
aws ec2 start-instances --instance-ids <InstanceId>

# Verify new state and public IP
aws ec2 describe-instances \
  --instance-ids <InstanceId> \
  --query "Reservations[*].Instances[*].[InstanceType,PublicDnsName,PublicIpAddress,State.Name]"
```
> **Screenshot:** COnsole — new `t3.micro` instance `running`
> <img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/7f1c93f7-a7c7-4597-a2bb-9d9f2140f4ac" />
>

Waited for `State.Name = running`, then opened `http://ec2-18-236-96-66.us-west-2.compute.amazonaws.com/cafe` — website functional. ✅

> **Screenshot:** Terminal — `describe-instances` output showing `t3.micro` + `running`
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/73510361-7e7a-4fa0-b344-78f8c6315708" />


> **Screenshot:** Browser — Café website loading after resize

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/b40a362f-ba7a-4693-b0f5-3402a6a836c3" />


---

## Task 2 — Estimate Costs with AWS Pricing Calculator

### Before Optimization

| Service | Configuration | Cost |
|---|---|---|
| EC2 | t3.small, On-Demand, Linux, 40 GB gp2 |  $18.38/mo |
| RDS | db.t3.micro, MariaDB, 20 GB gp2 | ~$14.71/mo |
| **Total** | | **$33.09/mo** |

### After Optimization

| Service | Configuration | Cost |
|---|---|---|
| EC2 | **t3.micro**, On-Demand, Linux, **20 GB gp2** | $9.19/mo |
| RDS | db.t3.micro, MariaDB, 20 GB gp2 | $14.71/mo |
| **Total** | | **$23.90/mo** |

### Projected Savings

```
Before:  $33.09 / month
After:   $23.90 / month
         ─────────────
Savings: $9.19 / month  (~$110.28 / year)
```

> **Screenshot:** AWS Pricing Calculator — estimate before optimization
>
> https://calculator.aws/#/estimate?id=11aaa928ae34e62ae9c235135ef1533550e40907
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/c1b8fb70-34bb-49e0-8337-edee2cc08b70" />


> **Screenshot:** AWS Pricing Calculator — estimate after optimization showing lower total
> 
> https://calculator.aws/#/estimate?id=f01b257ec12912f65634695069519dc680e4a55c
> <img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/125930a7-d1fb-41be-9d79-666172f40fdc" />




---

## Why Instance Must Be Stopped Before Resize

`modify-instance-attribute --instance-type` requires the instance to be in a **Stopped** state. AWS cannot change the underlying hardware type while the instance is running — it needs to be placed on a different physical host with the right resource profile.

This is the **stop → modify → start** lifecycle for instance type changes.

---

## Right-Sizing Decision Logic

| Factor | Before | After | Why it changed |
|---|---|---|---|
| Instance type | t3.small (2 vCPU, 2 GB RAM) | t3.micro (2 vCPU, 1 GB RAM) | DB process no longer runs on instance |
| EBS storage | 40 GB | 20 GB | 20 GB freed after removing MariaDB data |
| Workload | Web app + local DB | Web app only | DB moved to RDS |

---

## Reflection

In this lab I learned how to:

- **Right-size an EC2 instance via CLI** — the `stop → modify-instance-attribute → start` sequence is the standard procedure for changing instance type; understanding that the instance must be stopped first is critical for planning maintenance windows
- **Quantify the impact of architectural decisions** — migrating the database to RDS didn't just improve resilience; it also enabled a smaller, cheaper EC2 instance type, demonstrating that architectural choices have cascading cost effects
- **Use AWS Pricing Calculator to build a business case** — comparing before/after estimates with real configuration data produces a concrete cost savings number ($10.42/month, ~$125/year) that justifies the optimization effort to business stakeholders
- **Think in terms of waste elimination** — 20 GB of EBS storage occupied by a decommissioned database is pure cost with zero value; identifying and removing such waste is the first principle of cloud cost optimization

> Key takeaway: Cloud cost optimization is not just about choosing the cheapest option upfront — it's about continuously matching resources to actual workload requirements. When the workload changes (DB moved to RDS), the compute profile should change too. This right-sizing discipline, combined with the AWS Pricing Calculator for cost modeling, is the foundation of FinOps practice on AWS.

---

*Kateryna | AWS Cloud Practitioner Exam Prep*
