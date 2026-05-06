
# Lab: Build Your VPC and Launch a Web Server

**Course:** AWS Cloud Practitioner Exam Prep  
**Topic:** Amazon VPC — Custom Network Design + EC2 Web Server Deployment  

---

## Scenario

Build a complete custom VPC for a Fortune 100 customer, including public and private subnets across two Availability Zones, routing, a NAT gateway, a security group, and an EC2 instance running a web server.

**Final architecture:**

```
Internet
    │
Internet Gateway
    │
┌─────────────────────────────────┐
│           Lab VPC (10.0.0.0/16) │
│                                 │
│  Public Subnet 1  (10.0.0.0/24) │  AZ1
│  Public Subnet 2  (10.0.2.0/24) │  AZ2  ← Web Server 1 here
│                                 │
│  Private Subnet 1 (10.0.1.0/24) │  AZ1
│  Private Subnet 2 (10.0.3.0/24) │  AZ2
│                  ↑              │
│            NAT Gateway          │
└─────────────────────────────────┘
```

---

## Task 1 — Create VPC with Wizard

In **VPC Console → Create VPC → VPC and more**:

| Setting | Value |
|---|---|
| IPv4 CIDR | `10.0.0.0/16` |
| Availability Zones | 1 |
| Public subnets | 1 (`10.0.0.0/24`) |
| Private subnets | 1 (`10.0.1.0/24`) |
| NAT Gateway | In 1 AZ |
| VPC endpoints | None |

Named resources: `Lab VPC`, `Public Subnet 1`, `Private Subnet 1`, `Public Route Table`, `Private Route Table`.

> **Screenshot:** VPC Console → `Lab VPC` created successfully

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/841772d3-d0db-45da-b02c-e94cd0ae0756" />

> **Screenshot:** VPC Console → `Lab VPC` State Available
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/7e385f4a-299f-4883-9b46-1bb55a496e3b" />

---

## Task 2 — Create Additional Subnets (Second AZ)

Added two more subnets for high availability:

| Subnet | CIDR | AZ |
|---|---|---|
| Public Subnet 2 | `10.0.2.0/24` | No preference (AZ2) |
| Private Subnet 2 | `10.0.3.0/24` | No preference (AZ2) |

> **Screenshot:** VPC Console → Subnets 

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/07085282-7b4d-437c-be25-f6f5a4a41f78" />
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/f76ec52a-92f4-4d40-921f-b09ed02ba12a" />


---

## Task 3 — Associate Subnets to Route Tables

- **Public Route Table** → associated with `Public Subnet 2`
- **Private Route Table** → associated with `Private Subnet 2`

This ensures traffic from public subnets routes through the Internet Gateway, while private subnets route through the NAT Gateway.

> **Screenshot:** Public Route Table → Subnet associations tab

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/03513fe4-2a32-47c9-87db-37318fdb9cd0" />


---

## Task 4 — Create Security Group

In **VPC Console → Security Groups → Create security group**:

| Setting | Value |
|---|---|
| Name | `Web Security Group` |
| VPC | Lab VPC |
| Inbound rule | HTTP (port 80) from `Anywhere IPv4` |
| Description | Permit web requests |

> **Screenshot:** Web Security Group → Inbound rules showing HTTP/80

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/d4ca71ff-dca9-4395-b0a0-1ca93b695928" />


---

## Task 5 — Launch EC2 Web Server

In **EC2 Console → Launch instances**:

| Setting | Value |
|---|---|
| Name | `Web Server 1` |
| AMI | Amazon Linux 2 (HVM) |
| Instance type | `t3.micro` |
| VPC | Lab VPC |
| Subnet | Public Subnet 2 |
| Auto-assign public IP | Enabled |
| Security Group | Web Security Group |

**User Data script** (runs on launch):

```bash
#!/bin/bash
yum install -y httpd mysql php
wget https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/.../lab-app.zip
unzip lab-app.zip -d /var/www/html/
chkconfig httpd on
service httpd start
```

Waited for **status checks passed**, then opened `Public IPv4 DNS` in browser.

> **Screenshot:** EC2 Console → Web Server 1 status = **3/3 checks passed**

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/0a32fdcc-a1d8-4367-b8ef-8b139b381f93" />



> **Screenshot:** Browser — web application loaded via Public IPv4 DNS ✅

![web-server-live](screenshots/06-web-server-live.png)

---

## VPC Concepts — Quick Reference

| Component | Role |
|---|---|
| **Internet Gateway** | Enables internet access for public subnets |
| **NAT Gateway** | Allows private subnet instances to reach internet (outbound only) |
| **Public Subnet** | Has route `0.0.0.0/0 → IGW` |
| **Private Subnet** | Has route `0.0.0.0/0 → NAT GW` |
| **Security Group** | Stateful firewall at instance level |
| **Route Table** | Controls where subnet traffic is directed |

---

## Reflection

In this lab I learned how to:

- **Design a multi-AZ VPC from scratch** — understanding that two public and two private subnets across different AZs is the standard production pattern for high availability, not just a lab exercise
- **Distinguish Internet Gateway from NAT Gateway** — IGW allows bidirectional internet traffic for public subnets; NAT GW allows private subnet instances to initiate outbound connections without being publicly reachable
- **Use User Data to automate EC2 configuration** — the bootstrap script installs Apache and deploys the app automatically at launch, which is the foundation of infrastructure-as-code thinking
- **Connect all networking layers** — VPC → subnets → route tables → security group → EC2 must all be correctly configured for the web server to be reachable; any single misconfiguration breaks the chain

> Key takeaway: Building a VPC manually makes the "why" behind each component visible. In production, this same architecture is deployed via CloudFormation or Terraform — but understanding the manual steps is essential for troubleshooting and for the CLF-C02 exam.

---

*Kateryna | AWS Cloud Practitioner Exam Prep*
