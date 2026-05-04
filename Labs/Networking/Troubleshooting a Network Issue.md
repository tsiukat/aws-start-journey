# Lab: Troubleshooting a Network Issue in AWS VPC

**Course:** AWS Cloud Practitioner Exam Prep  
**Topic:** VPC Networking — Troubleshooting Connectivity  
---

## Scenario

A customer (Ana, contractor) created an Apache server on EC2 but:
- Cannot **ping** it from outside
- Cannot **reach it in the browser** via its public IP

**My role:** Cloud Support Engineer — reproduce the issue in a replica VPC and identify what is blocking the connection.

**Customer architecture:**

```
Internet → Internet Gateway → Public Subnet → EC2 Instance (Apache/httpd)
```

---

## Task 1 — Connect to EC2 via SSH

Connected to the Amazon Linux EC2 instance using SSH with a `.pem` key:

```bash
chmod 400 labsuser.pem
ssh -i labsuser.pem ec2-user@<public-ip>
```

> **Screenshot:** Terminal showing successful SSH login to EC2

![ssh-connected](screenshots/01-ssh-connected.png)

---

## Task 2 — Install and Start Apache (httpd)

**Check service status:**
```bash
sudo systemctl status httpd.service
```
Result: `inactive` — httpd was installed but not running.

**Start the service:**
```bash
sudo systemctl start httpd.service
sudo systemctl status httpd.service
```
Result: `active (running)`

**Test in browser:**
```
http://<PUBLIC-IP-OF-INSTANCE>
```
Page did **not load** — confirming a network-level block despite Apache running.

> **Screenshot:** Terminal showing httpd status = `active`

![httpd-active](screenshots/02-httpd-active.png)

> **Screenshot:** Browser showing the page fails to load (connection timeout)

![browser-blocked](screenshots/03-browser-blocked.png)

---

## Task 3 — Investigate VPC Configuration

Checked each networking layer in **VPC Console → left navigation panel**:

### Checklist

| Component | What to verify |
|---|---|
| **Subnet** | Route table associated to the correct public subnet? |
| **Route Table** | Route `0.0.0.0/0 → igw-xxxxx` present? |
| **Internet Gateway** | Exists AND attached to the VPC? |
| **Security Group** | Inbound rule allowing **HTTP (port 80)** from `0.0.0.0/0`? |
| **Network ACL** | Inbound/outbound rules allowing HTTP traffic? |

> **Screenshot:** VPC Console → Route Table showing routes (or missing route)

![route-table](screenshots/04-route-table.png)

> **Screenshot:** Security Group inbound rules — before and after fix

![security-group-fix](screenshots/05-security-group-fix.png)

**After fixing the misconfiguration, Apache test page loaded successfully:**

> **Screenshot:** Browser showing Apache HTTP Server test page ✅

![apache-working](screenshots/06-apache-working.png)

---

## Root Cause Summary

Apache itself was running correctly. The connection was blocked at the **network level** — most commonly one or more of:

- Missing inbound rule for **port 80 (HTTP)** in the Security Group
- Missing or incorrect route to the **Internet Gateway** in the Route Table
- Internet Gateway not attached to the VPC

> **Key insight:** In AWS, connectivity requires *every layer* to be configured correctly — EC2, Security Group, NACL, Route Table, and Internet Gateway must all align.

---

## Reflection

In this lab I learned how to:

- **Systematically troubleshoot network connectivity** in AWS by checking each VPC layer independently — from the Internet Gateway down to the Security Group
- **Distinguish between application-level and network-level failures** — Apache was running fine; the problem was purely in VPC configuration
- **Understand the role of Security Groups as stateful firewalls** — an explicit inbound rule for HTTP (port 80) must be added; there is no "allow all" by default
- **Read a VPC architecture diagram** and map it to actual AWS Console resources to identify misconfigurations

> Key takeaway: AWS networking follows a *defense-in-depth* model — multiple independent layers (SG, NACL, route table, IGW) must each be correctly configured for traffic to flow. This is core knowledge for both the CLF-C02 exam and real cloud support scenarios.

---

*Kateryna | AWS Cloud Practitioner Exam Prep*
