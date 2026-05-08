# Lab: Configuring a VPC

**Course:** AWS Cloud Practitioner Exam Prep  
**Topic:** Amazon VPC — Manual Build, Bastion Server, NAT Gateway, Private Subnet Access  


---

## Overview

Built a complete VPC from scratch — component by component — without the wizard. Configured public and private subnets, an internet gateway, a NAT gateway, route tables, and a bastion server pattern to securely access private resources.

**Final architecture:**

```
Internet
    │
Internet Gateway (Lab IGW)
    │
┌──────────────────────────────────────────────┐
│                Lab VPC (10.0.0.0/16)          │
│                                              │
│  ┌─────────────────────────────────────┐    │
│  │  Public Subnet (10.0.0.0/24)        │    │
│  │  → Route: 0.0.0.0/0 → IGW          │    │
│  │  [Bastion Server]  [NAT Gateway]    │    │
│  └─────────────────────────────────────┘    │
│                    │                         │
│  ┌─────────────────────────────────────┐    │
│  │  Private Subnet (10.0.2.0/23)       │    │
│  │  → Route: 0.0.0.0/0 → NAT GW       │    │
│  │  [Private Instance]                 │    │
│  └─────────────────────────────────────┘    │
└──────────────────────────────────────────────┘
```

---

## Task 1 — Create VPC

In **VPC Console → Create VPC → VPC only**:

| Setting | Value |
|---|---|
| Name | `Lab VPC` |
| IPv4 CIDR | `10.0.0.0/16` |
| IPv6 | None |
| Tenancy | Default |

After creation: **Actions → Edit VPC settings → Enable DNS hostnames** — so EC2 instances get public DNS names automatically.

> **Screenshot:** VPC Console → `Lab VPC` created with DNS hostnames enabled
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/7ea9dc80-5eda-43f1-a3ed-fa62089d1fe1" />


---

## Task 2 — Create Subnets

### Public Subnet

| Setting | Value |
|---|---|
| Name | `Public Subnet` |
| CIDR | `10.0.0.0/24` |
| AZ | First AZ in list |
| Auto-assign public IPv4 | **Enabled** |

### Private Subnet

| Setting | Value |
|---|---|
| Name | `Private Subnet` |
| CIDR | `10.0.2.0/23` |
| AZ | Same first AZ |

Private subnet is /23 (twice as large) — most resources belong in private subnets.

> **Screenshot:** VPC Console → Subnets — both subnets listed

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/eda25937-7d7e-4dc2-bf8b-e8ee154f43df" />


---

## Task 3 — Create and Attach Internet Gateway

Created `Lab IGW` → **Actions → Attach to VPC → Lab VPC**.

> **Screenshot:** Internet Gateways → `Lab IGW` state = **Attached**
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/23525777-4569-419a-b9a9-687f59c9b3a3" />


---

## Task 4 — Configure Route Tables

### Private Route Table (existing — renamed)
- Route: `10.0.0.0/16 → local` only (no internet access)

### Public Route Table (new)

| Route | Target |
|---|---|
| `10.0.0.0/16` | local |
| `0.0.0.0/0` | `Lab IGW` |

Associated `Public Subnet` to the Public Route Table.

> **Screenshot:** Public Route Table → Routes tab showing IGW route + Subnet Associations

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/784b52ba-6810-4b9c-a0e1-79d1aa69c454" />


---

## Task 5 — Launch Bastion Server in Public Subnet

In **EC2 Console → Launch instances**:

| Setting | Value |
|---|---|
| Name | `Bastion Server` |
| AMI | Amazon Linux 2023 |
| Instance type | `t3.micro` |
| VPC | Lab VPC |
| Subnet | Public Subnet |
| Auto-assign public IP | Enabled |
| Security Group | `Bastion Security Group` — SSH from Anywhere |

> **Screenshot:** EC2 Console → Bastion Server status = **Running** in Public Subnet
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/35fbe8d6-1382-470d-8c12-8b39d28854ff" />

---

## Task 6 — Create NAT Gateway

In **VPC Console → NAT gateways → Create NAT gateway**:

| Setting | Value |
|---|---|
| Name | `Lab NAT gateway` |
| Subnet | Public Subnet |
| Elastic IP | Allocated (new) |

Added route to **Private Route Table**:

| Route | Target |
|---|---|
| `0.0.0.0/0` | `Lab NAT gateway` |

Private subnet instances can now reach the internet outbound — without being publicly reachable inbound.

> **Screenshot:** Private Route Table → Routes showing NAT gateway route

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/00e298e5-f93b-42be-b4e9-33f925ca5165" />



---

## Optional Challenge — Bastion → Private Instance → ping amazon.com

Launched `Private Instance` in Private Subnet (no public IP), then:

```bash
# Step 1: Connect to Bastion Server via EC2 Instance Connect

# Step 2: From Bastion, SSH into Private Instance
ssh PRIVATE-IP      # e.g. ssh 10.0.2.123
# password: lab-password

# Step 3: Test outbound internet via NAT gateway
ping -c 3 amazon.com
```

Expected output:
```
64 bytes from 176.32.98.166: icmp_seq=1 ttl=222 time=79.2 ms
64 bytes from 176.32.98.166: icmp_seq=2 ttl=222 time=79.2 ms
64 bytes from 176.32.98.166: icmp_seq=3 ttl=222 time=79.0 ms
```

Successful ping confirms the Private Instance reached the internet through the NAT gateway — not directly.

> **Screenshot:** Terminal — `ping amazon.com` from Private Instance showing successful replies

![ping-success](screenshots/07-ping-success.png)

---

## IGW vs NAT Gateway — Core Difference

| Component | Direction | Used by |
|---|---|---|
| **Internet Gateway** | Inbound + Outbound | Public subnet instances (bidirectional) |
| **NAT Gateway** | Outbound only | Private subnet instances (no inbound from internet) |

The bastion pattern: public-facing jump host → private instance. No direct public access to private resources.

---

## Reflection

In this lab I learned how to:

- **Build a VPC manually component by component** — creating the VPC, then subnets, then IGW, then route tables, then NAT gateway in sequence; each step depends on the previous, which makes the dependency chain between components concrete
- **Understand that a "public" subnet requires both auto-assign public IP AND a route table pointing to an IGW** — the subnet name alone means nothing without the route
- **Implement the bastion server pattern** — connecting to a private instance by first SSH-ing into a public bastion, then jumping to the private IP; this is the standard secure access pattern for private resources in production
- **Verify NAT gateway functionality with `ping`** — outbound internet from a private instance with no public IP proves that the NAT gateway is forwarding traffic correctly, and that the private route table is configured properly

> Key takeaway: Every component of a VPC has a specific job, and none of them work in isolation. IGW handles public bidirectional traffic, NAT handles private outbound-only traffic, route tables direct where packets go, and security groups control what can reach the instance. Mastering this mental model is essential for both the CLF-C02 exam and for understanding any AWS architecture diagram.

---

*Kateryna | AWS Cloud Practitioner Exam Prep*
