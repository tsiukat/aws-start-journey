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

<!-- Add screenshot: instance state Running + 2/2 Status Checks passed -->
![EC2 Instance Running](screenshots/task1-instance-running.png)

---

### Task 2 — Monitoring and Status Checks

<!-- Add screenshot: Status Checks tab -->
![Status Checks](screenshots/task2-status-checks.png)

<!-- Add screenshot: Instance Screenshot from the console -->
![Instance Screenshot](screenshots/task2-instance-screenshot.png)

---

### Task 3 — Security Group: Inbound Rule HTTP

<!-- Add screenshot: HTTP port 80 inbound rule added -->
![Inbound Rule HTTP](screenshots/task3-security-group-http.png)

<!-- Add screenshot: browser showing "Hello From Your Web Server!" -->
![Web Server Response](screenshots/task3-web-server-hello.png)

---

### Task 4 — Instance Type and EBS Volume Resize

<!-- Add screenshot: instance type changed to t3.small -->
![Change Instance Type](screenshots/task4-instance-type-change.png)

<!-- Add screenshot: Modify Volume 8 GiB → 10 GiB -->
![Resize EBS Volume](screenshots/task4-ebs-resize.png)

---

### Task 5 — Termination Protection

<!-- Add screenshot: error "Failed to terminate an instance" -->
![Termination Error](screenshots/task5-termination-error.png)

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

This lab gave me my first hands-on experience with EC2 — from launching an instance to terminating it. The most tangible lesson came in Task 3: the web server was already running, but completely unreachable until I added the HTTP inbound rule. That is **default deny** in action — a core principle of cloud security that I now understand not just in theory, but from actually being locked out.

Resizing the instance required stopping it first, which makes sense architecturally — instance type is tied to the underlying physical host. In a real-world FinTech environment, this means any vertical scaling requires planning a maintenance window, which has direct implications for SLA commitments.

The **User Data script** was an elegant automation pattern: instead of manually configuring a server after launch, the instance configures itself on startup. This is the foundation of scalable, repeatable infrastructure — and a stepping stone toward understanding Auto Scaling Groups.

Termination Protection felt like a small detail, but it carries real weight in production. Accidental deletion of a running instance can mean data loss, downtime, and incident response — a few clicks of configuration that cost nothing but prevent a lot.

> 💡 **Key Insight:** In cloud architecture, security, scalability, and reliability are not separate features — they are interconnected design decisions that need to be considered at the moment a resource is launched, not added as an afterthought.

---

*Author: Kateryna | AWS Cloud Practitioner Exam Prep*
