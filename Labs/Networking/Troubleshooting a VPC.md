# Lab: Troubleshooting a VPC

**Course:** AWS Cloud Practitioner Exam Prep  
**Topic:** VPC Troubleshooting — Flow Logs, Route Tables, Security Groups, Network ACLs  

---

## Overview

Diagnosed and fixed two separate networking failures blocking access to a café web server, using only AWS CLI commands. Also created VPC Flow Logs, captured traffic data during troubleshooting, and analyzed the logs to confirm that rejected connection attempts were recorded.

**Environment:**

```
CLI Host VPC ──── (admin access) ──── VPC1
                                        │
                               Public Subnet
                                        │
                               [Cafe Web Server EC2]
                               WebServerIP: <public IP>
```

---

## Task 1 — Configure CLI Host

Connected to **CLI Host** via EC2 Instance Connect, configured AWS CLI:

```bash
aws configure
# AccessKey / SecretKey / us-west-2 / json
```

---

## Task 2 — Create VPC Flow Logs

### Create S3 bucket for log storage

```bash
aws s3api create-bucket \
  --bucket flowlog061188 \
  --region us-west-2 \
  --create-bucket-configuration LocationConstraint='us-west-2'
```

### Get VPC1 ID

```bash
aws ec2 describe-vpcs \
  --query 'Vpcs[*].[VpcId,Tags[?Key==`Name`].Value,CidrBlock]' \
  --filters "Name=tag:Name,Values='VPC1'"
# Result: vpc-07baf985ba6841ede
```

### Enable Flow Logs on VPC1 → S3

```bash
aws ec2 create-flow-logs \
  --resource-type VPC \
  --resource-ids vpc-07baf985ba6841ede \
  --traffic-type ALL \
  --log-destination-type s3 \
  --log-destination arn:aws:s3:::flowlog061188
```

Confirmed with `aws ec2 describe-flow-logs` 
> **Screenshot:** Terminal — `describe-flow-logs` 

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/bec63839-e42a-4122-abae-afcb5652f7d7" />


---

## Task 3 — Troubleshooting Challenge #1: Website Not Loading

Opened `http://WebServerIP` → connection timeout. EC2 Instance Connect → failed.

### Investigation via CLI

```bash
# Inspect instance state, SGs, subnet
aws ec2 describe-instances \
  --filter "Name=ip-address,Values='44.251.156.111'" \
  --query 'Reservations[*].Instances[*].[State,PrivateIpAddress,InstanceId,SecurityGroups,SubnetId,KeyName]'

# Check route table for the public subnet
aws ec2 describe-route-tables \
  --filter "Name=association.subnet-id,Values='subnet-0b221f625217967d8'"
```

**Root cause:** Public subnet route table was **missing the route to the Internet Gateway** (`0.0.0.0/0 → IGW`).

### Fix

```bash
aws ec2 create-route \
  --route-table-id rtb-0c8b3cbd477213e2b \
  --destination-cidr-block 0.0.0.0/0 \
  --gateway-id igw-0afbfc13f682a8534
```

**Result:** Website loaded — "Hello From Your Web Server!" ✅

> **Screenshot:** Terminal — `create-route` success + browser showing web server response
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/e0bcc555-7337-4c2c-85fd-d494e33b9508" />
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/a04348b5-a755-4eed-a6aa-48080f0ff192" />



---

## Task 3 — Troubleshooting Challenge #2: SSH Still Failing

Web server accessible but EC2 Instance Connect still failed. Security Group already allowed port 22.

### Investigation via CLI

```bash
# Check Network ACL for the public subnet
aws ec2 describe-network-acls \
  --filter "Name=association.subnet-id,Values='subnet-0b221f625217967d8'" \
  --query 'NetworkAcls[*].[NetworkAclId,Entries]'
```

**Root cause:** Network ACL had a **DENY rule blocking SSH (port 22)** — overriding the Security Group's allow rule.
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/945fe1e1-b0ac-4a30-a032-9f68548c37a2" />

> Key distinction: Security Groups are stateful (allow-only). Network ACLs are **stateless** and evaluated before Security Groups — a DENY in the NACL blocks traffic regardless of SG settings.

### Fix

```bash
aws ec2 delete-network-acl-entry
--network-acl-id 'acl-0f613ad0c147c636f'
--ingress --rule-number 40
```

**Result:** EC2 Instance Connect succeeded — `hostname` returned `web-server` ✅

> **Screenshot:** Terminal — successful EC2 Instance Connect + hostname output **ACTIVE**
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/c241c1c9-eb72-4e4d-8508-06782983c0ef" />


---

## Task 4 — Analyze VPC Flow Logs

### Download and extract logs from S3

```bash
mkdir flowlogs && cd flowlogs
aws s3 cp s3://flowlog061188/ . --recursive
cd AWSLogs/166998533480/vpcflowlogs/us-west-2/2026/05/08/
gunzip *.gz
```

### Inspect log structure

```bash
head <filename>
# Columns: version, account, interface, srcaddr, dstaddr, srcport, dstport, protocol, packets, bytes, start, end, action, log-status
```

### Query for rejected SSH attempts

```bash
# All REJECT events
grep -rn REJECT . | wc -l

# REJECTs involving port 22
grep -rn 22 . | grep REJECT
```

Matched log entries to **my failed SSH attempts** — count matched number of connection attempts.

### Translate Unix timestamp to human-readable

```bash
date -d @1778241794
# Returns: exact time of the rejected connection attempt
```

> **Screenshot:** Terminal — filtered REJECT log entries showing my IP and port 22

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/13c2ee21-7f06-4da8-ac33-ed5d7b74d786" />


---

## Troubleshooting Layers — What Was Checked

| Layer | Tool | Issue Found |
|---|---|---|
| Instance state | `describe-instances` | Running ✅ |
| Route table | `describe-route-tables` | ❌ Missing IGW route → **Fixed** |
| Security Group | `describe-security-groups` | Port 22 allowed ✅ |
| Network ACL | `describe-network-acls` | ❌ DENY rule on port 22 → **Fixed** |
| Flow Logs | `grep` on `.log` files | Rejected attempts captured ✅ |

---

## Security Group vs Network ACL

| Feature | Security Group | Network ACL |
|---|---|---|
| Level | Instance | Subnet |
| Statefulness | Stateful (return traffic auto-allowed) | Stateless (both directions must be explicitly allowed) |
| Rules | Allow only | Allow and Deny |
| Evaluation order | After NACL | Before Security Group |

---

## Reflection

In this lab I learned how to:

- **Use VPC Flow Logs as a forensic tool** — capturing all IP traffic before starting to troubleshoot meant I had evidence of every rejected connection attempt, which confirmed exactly what was blocked and when
- **Diagnose networking issues layer by layer via CLI** — `describe-instances` → `describe-route-tables` → `describe-security-groups` → `describe-network-acls` is the correct diagnostic sequence, each layer ruling out one possible cause
- **Understand that Network ACLs override Security Groups** — a DENY rule at the subnet level blocked SSH even though the Security Group allowed it; this is a critical distinction for both troubleshooting and the CLF-C02 exam
- **Analyze flow logs with `grep` and Unix timestamp conversion** — filtering by port, action (REJECT), and source IP narrows thousands of log lines down to the exact events that matter

> Key takeaway: In AWS networking, traffic passes through multiple independent gatekeepers — route table, NACL, Security Group — in that order. A misconfiguration at any one layer breaks connectivity. VPC Flow Logs make this failure visible after the fact; systematic CLI investigation makes it diagnosable in real time.

---

*Kateryna | AWS Cloud Practitioner Exam Prep*
