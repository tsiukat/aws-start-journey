# Lab 01 — Introduction to Amazon EC2

> **Course:** AWS Cloud Practitioner (CLF-C02)  
> **Topic:** Launching, Monitoring, and Managing EC2 Instances  
> **Date:** <SUBMITTED: Mar 11, 2026>

---

## Table of Contents

1. [Lab Objective](#lab-objective)
2. [Tasks Completed](#tasks-completed)
3. [Screenshots](#screenshots)
4. [Key Takeaways](#key-takeaways)
5. [My Reflection](#my-reflection)

---

## Lab Objective

Get hands-on experience with Amazon EC2: launch a web server on a cloud instance, configure network security, resize compute and storage resources, and test termination protection.

---

## Tasks Completed

### ✅ Task 1 — Launch an EC2 Instance
- AMI selected: **Amazon Linux 2023**
- Instance type: **t3.micro** (2 vCPU, 1 GiB RAM)
- Configured **Security Group** (Web Server security group) — SSH access intentionally removed to improve security
- Attached **User Data script** to automatically install Apache HTTP Server on boot
- Enabled **Termination Protection**

### ✅ Task 2 — Monitor the Instance
- Reviewed **Status Checks** (System reachability + Instance reachability)
- Explored metrics in **Amazon CloudWatch**
- Captured an **Instance Screenshot** via the EC2 console

### ✅ Task 3 — Update Security Group and Access the Web Server
- Added Inbound rule: **HTTP (port 80), Source: Anywhere-IPv4**
- Web server responded successfully: `Hello From Your Web Server!`

### ✅ Task 4 — Resize Instance Type and EBS Volume
- Stopped instance → changed type from **t3.micro → t3.small**
- Expanded EBS volume from **8 GiB → 10 GiB**
- Restarted the instance

### ✅ Task 5 — Test Termination Protection
- Attempted to terminate the instance — received the expected error: `Failed to terminate an instance`
- Disabled protection → instance terminated successfully

---

## Screenshots

### Task 1 — EC2 Instance Running

<img width="1647" height="186" alt="screenshots task1-instance-running" src="https://github.com/user-attachments/assets/2d7b2b65-8227-4765-b4d6-00b68e6a22f0" />


---

### Task 2 — Monitoring and Status Checks

<img width="1267" height="57" alt="screenshots task2-status-checks" src="https://github.com/user-attachments/assets/0d4c250e-7127-4350-a71c-319ce32835c2" />
<img width="1628" height="519" alt="screenshots task2-instance-screenshot" src="https://github.com/user-attachments/assets/92f83d93-0f8c-4af0-aaff-ee16d7e6a3ab" />

---

### Task 3 — Security Group: Inbound Rule HTTP

<img width="1889" height="496" alt="screenshots task3-Security Group Inbound Rule HTTP" src="https://github.com/user-attachments/assets/50d15bc1-2df7-4b57-bbba-063965a5702d" />

<img width="815" height="230" alt="screenshots task3-Security Group Inbound Rule HTTP1" src="https://github.com/user-attachments/assets/8504d9c0-7a5f-4be1-8fe5-690f69d73966" />

---

### Task 4 — Instance Type and EBS Volume Resize

<img width="1653" height="836" alt="screenshots task4-instance-type-change" src="https://github.com/user-attachments/assets/c75fd0b9-3c15-40d0-bfdf-cb5c449917a4" />

<img width="1665" height="347" alt="screenshots task4-ebs-resize" src="https://github.com/user-attachments/assets/89c911c3-6816-4ba0-8a4e-47f62ee8fea1" />

---

### Task 5 — Termination Protection

<img width="1670" height="821" alt="screenshots task5-termination-error" src="https://github.com/user-attachments/assets/79f37137-e92d-4827-add0-0200f1a1acb5" />


---

## Key Takeaways

| Concept | What I Learned |
|---------|----------------|
| **AMI** | A template for launching an instance — OS + configuration + block device mapping |
| **Instance Type** | Defines compute resources (CPU, RAM); can be changed after stopping the instance |
| **Security Group** | Acts as a virtual firewall — controls inbound/outbound traffic at the instance level |
| **User Data** | A bash script executed on first boot — enables automated configuration at launch |
| **EBS** | A network-attached disk — persists independently of the instance state; can be resized |
| **Termination Protection** | Prevents accidental deletion; must be explicitly disabled before terminating |
| **CloudWatch** | Collects instance metrics (basic monitoring = every 5 minutes by default) |
| **Status Checks** | Automated hardware- and OS-level health checks run by AWS on every instance |

---

## My Reflection

This lab gave me my first hands-on experience with EC2 from launching an instance to terminating it. The most tangible lesson came in Task 3: the web server was already running, but completely unreachable until I added the HTTP inbound rule. That is **default deny** in action - a core principle of cloud security that I now understand not just in theory, but from actually being locked out.

Resizing the instance required stopping it first, which makes sense architecturally, instance type is tied to the underlying physical host. In a real-world FinTech environment, this means any vertical scaling requires planning a maintenance window, which has direct implications for SLA commitments.

Termination Protection felt like a small detail, but it carries real weight in production. Accidental deletion of a running instance can mean data loss, downtime, and incident response, a few clicks of configuration that cost nothing but prevent a lot.

> 💡 **Key Insight:** In cloud architecture, security, scalability, and reliability are not separate features — they are interconnected design decisions that need to be considered at the moment a resource is launched, not added as an afterthought.

---

*Author: Kateryna | AWS Cloud Practitioner Exam Prep*
