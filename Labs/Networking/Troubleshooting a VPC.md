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
  --bucket flowlog###### \
  --region us-west-2 \
  --create-bucket-configuration LocationConstraint='us-west-2'
```

### Get VPC1 ID

```bash
aws ec2 describe-vpcs \
  --query 'Vpcs[*].[VpcId,Tags[?Key==`Name`].Value,CidrBlock]' \
  --filters "Name=tag:Name,Values='VPC1'"
```

### Enable Flow Logs on VPC1 → S3

```bash
aws ec2 create-flow-logs \
  --resource-type VPC \
  --resource-ids <vpc-id> \
  --traffic-type ALL \
  --log-destination-type s3 \
  --log-destination arn:aws:s3:::flowlog######
```

Confirmed with `aws ec2 describe-flow-logs` → status: **ACTIVE**

> **Screenshot:** Terminal — `describe-flow-logs` showing ACTIVE status

![flow-logs-active](screenshots/01-flow-logs-active.png)

---

## Task 3 — Troubleshooting Challenge #1: Website Not Loading

Opened `http://WebServerIP` → connection timeout. EC2 Instance Connect → failed.

### Investigation via CLI

```bash
# Inspect instance state, SGs, subnet
aws ec2 describe-instances \
  --filter "Name=ip-address,Values='<WebServerIP>'" \
  --query 'Reservations[*].Instances[*].[State,PrivateIpAddress,InstanceId,SecurityGroups,SubnetId,KeyName]'

# Check route table for the public subnet
aws ec2 describe-route-tables \
  --filter "Name=association.subnet-id,Values='<VPC1PubSubnetID>'"
```

**Root cause:** Public subnet route table was **missing the route to the Internet Gateway** (`0.0.0.0/0 → IGW`).

### Fix

```bash
aws ec2 create-route \
  --route-table-id <RouteTableId> \
  --destination-cidr-block 0.0.0.0/0 \
  --gateway-id <IGW-id>
```

**Result:** Website loaded — "Hello From Your Web Server!" ✅

> **Screenshot:** Terminal — `create-route` success + browser showing web server response

![route-fixed](screenshots/02-route-fixed.png)

---

## Task 3 — Troubleshooting Challenge #2: SSH Still Failing

Web server accessible but EC2 Instance Connect still failed. Security Group already allowed port 22.

### Investigation via CLI

```bash
# Check Network ACL for the public subnet
aws ec2 describe-network-acls \
  --filter "Name=association.subnet-id,Values='<VPC1PublicSubnetID>'" \
  --query 'NetworkAcls[*].[NetworkAclId,Entries]'
```

**Root cause:** Network ACL had a **DENY rule blocking SSH (port 22)** — overriding the Security Group's allow rule.

> Key distinction: Security Groups are stateful (allow-only). Network ACLs are **stateless** and evaluated before Security Groups — a DENY in the NACL blocks traffic regardless of SG settings.

### Fix

```bash
aws ec2 delete-network-acl-entry \
  --network-acl-id <NetworkAclId> \
  --ingress \
  --rule-number <RuleNumber>
```

**Result:** EC2 Instance Connect succeeded — `hostname` returned `web-server` ✅

> **Screenshot:** Terminal — successful EC2 Instance Connect + hostname output

![ssh-fixed](screenshots/03-ssh-fixed.png)

---

## Task 4 — Analyze VPC Flow Logs

### Download and extract logs from S3

```bash
mkdir flowlogs && cd flowlogs
aws s3 cp s3://flowlog######/ . --recursive
cd AWSLogs/<AccountID>/vpcflowlogs/us-west-2/yyyy/mm/dd/
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

# Filtered to my IP address only
grep -rn 22 . | grep REJECT | grep <my-ip>
```

Matched log entries to **my failed SSH attempts** — count matched number of connection attempts.

### Translate Unix timestamp to human-readable

```bash
date -d @<timestamp>
# Returns: exact time of the rejected connection attempt
```

> **Screenshot:** Terminal — filtered REJECT log entries showing my IP and port 22

![flow-log-analysis](screenshots/04-flow-log-analysis.png)

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
