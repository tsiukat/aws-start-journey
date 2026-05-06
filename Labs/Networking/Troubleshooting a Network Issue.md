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
http://35.92.233.41
```
Page did **not load** — confirming a network-level block despite Apache running.

> **Screenshot:** Terminal showing httpd status = `active`
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/2dc3553a-b0ee-4d4a-8233-a19a3afdc8f4" />


> **Screenshot:** Browser showing the page fails to load (connection timeout)

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/84ae8380-8241-4f28-957b-696e98035310" />


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


> **Screenshot:** Security Group inbound rules — added **HTTP (port 80)** 

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/451b4815-7e33-4bda-84b4-6094a55713a5" />


**After fixing the misconfiguration, Apache test page loaded successfully:**

> **Screenshot:** Browser showing Apache HTTP Server test page ✅

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/b96494ac-9e1a-4671-af54-c414a3045b42" />

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
