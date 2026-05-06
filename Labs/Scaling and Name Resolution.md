# Lab: Scaling and Load Balancing Your Architecture

**Course:** AWS Cloud Practitioner Exam Prep  
**Topic:** Elastic Load Balancing + EC2 Auto Scaling + CloudWatch Alarms  
---

## Overview

Transformed a single EC2 instance into a highly available, automatically scaling web application using:
- **Application Load Balancer (ALB)** — distributes traffic across instances and AZs
- **EC2 Auto Scaling** — launches/terminates instances based on CPU load
- **CloudWatch Alarms** — triggers scaling actions at defined thresholds

**Architecture — before and after:**

```
BEFORE:
[Internet] → [Web Server 1 - Public Subnet]

AFTER:
[Internet] → [Application Load Balancer]
                    ↙            ↘
         [EC2 - Private     [EC2 - Private
          Subnet 1 / AZ1]    Subnet 2 / AZ2]
         (Auto Scaling Group: min 2, max 4)
```

---

## Task 1 — Create AMI from Web Server 1

In **EC2 Console → Instances → Web Server 1 → Actions → Image and templates → Create image**:

| Setting | Value |
|---|---|
| Image name | `Web Server AMI` |
| Description | Lab AMI for Web Server |

This captures the configured boot disk so Auto Scaling can launch identical instances.

> **Screenshot:** EC2 Console → AMIs — `Web Server AMI` status = **Available**

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/7d1a8ae3-c5e4-4c5b-bb96-954437313820" />


---

## Task 2 — Create Application Load Balancer

In **EC2 Console → Load Balancers → Create load balancer → Application Load Balancer**:

| Setting | Value |
|---|---|
| Name | `LabELB` |
| VPC | Lab VPC |
| Subnets | Public Subnet 1 (AZ1) + Public Subnet 2 (AZ2) |
| Security Group | Web Security Group (HTTP/80) |
| Target Group | `lab-target-group` → Instances |

> **Screenshot:** EC2 Console → Load Balancers → `LabELB` state = **Active**

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/d5d32f50-919c-41cf-a117-10f5abe9071f" />


---

## Task 3 — Create Launch Template

In **EC2 Console → Launch Templates → Create launch template**:

| Setting | Value |
|---|---|
| Name | `lab-app-launch-template` |
| AMI | Web Server AMI (My AMIs tab) |
| Instance type | `t3.micro` |
| Key pair | Don't include |
| Security Group | Web Security Group |

> **Screenshot:** Launch Templates — `lab-app-launch-template` created

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/38c9c0cd-d07d-4d12-b121-68371cdeffd8" />


---

## Task 4 — Create Auto Scaling Group

In **EC2 Console → Auto Scaling Groups → Create**:

| Setting | Value |
|---|---|
| Name | `Lab Auto Scaling Group` |
| Launch template | lab-app-launch-template |
| VPC | Lab VPC |
| Subnets | **Private** Subnet 1 + Private Subnet 2 |
| Load balancer | Attach to `lab-target-group` |
| Health check | ELB |
| Desired / Min / Max | **2 / 2 / 4** |
| Scaling policy | Target tracking — Average CPU = **50%** |
| Instance tag | `Name: Lab Instance` |

> **Screenshot:** Auto Scaling Group → Activity tab — 2 instances launched
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/2151d729-aca4-4f56-88f2-ed59c99678db" />
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/748e160a-54c2-4d49-87a1-d2f91300607a" />



---

## Task 5 — Verify Load Balancing

Checked **Target Groups → lab-target-group → Registered targets** — both Lab Instances showed **healthy** status.

Opened the ALB DNS name in browser → Load Test application loaded, confirming the load balancer routed traffic to a healthy EC2 instance in a private subnet.

> **Screenshot:** Target Group → both instances Health status = **healthy**

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/e81a71c0-938d-443e-aa85-0bcdf54076da" />


> **Screenshot:** Browser — Load Test application served via ALB DNS

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/b91a1b8d-62ff-4327-8b74-f1e3cd349526" />


---

## Task 6 — Trigger and Observe Auto Scaling

Activated the **Load Test** function in the app to spike CPU across all instances.

Monitored in **CloudWatch → All Alarms**:

| Alarm | Trigger | Action |
|---|---|---|
| `AlarmHigh` | CPU > 50% for 3 min | Scale **out** — add instances |
| `AlarmLow` | CPU < 50% | Scale **in** — remove instances |

After ~5 minutes, `AlarmHigh` entered **In alarm** state → Auto Scaling launched additional instances (up to 4 max).

> **Screenshot:** CloudWatch → AlarmHigh state = **In alarm**

![cloudwatch-alarm](screenshots/07-cloudwatch-alarm.png)

> **Screenshot:** EC2 Console → more than 2 `Lab Instance` instances running

![scaled-out](screenshots/08-scaled-out.png)

---

## Key Concepts

| Component | Role |
|---|---|
| **AMI** | Snapshot of configured instance — used as template for new instances |
| **Launch Template** | Defines instance config (AMI, type, SG) for Auto Scaling |
| **Auto Scaling Group** | Manages fleet size — launches/terminates based on policy |
| **Target Tracking Policy** | Keeps average CPU at target (50%) by adjusting instance count |
| **ALB** | Routes incoming requests across all healthy instances |
| **CloudWatch Alarm** | Watches metric → triggers scaling action when threshold crossed |

---

## Reflection

In this lab I learned how to:

- **Understand the relationship between AMI → Launch Template → Auto Scaling Group** — each layer depends on the previous one; the AMI captures the server state, the template defines the instance config, and the ASG manages the fleet
- **Place Auto Scaling instances in private subnets** — instances don't need public IPs because all traffic arrives through the load balancer, which sits in public subnets; this is the secure production pattern
- **Configure target tracking scaling** — instead of manually setting "add 1 instance when CPU > 80%", target tracking continuously adjusts capacity to maintain the desired metric, which is simpler and more responsive
- **Observe scaling in real time via CloudWatch** — watching `AlarmHigh` transition from OK → In alarm and then seeing new EC2 instances appear made the scaling lifecycle concrete and observable

> Key takeaway: ELB + Auto Scaling together deliver the two core AWS promises — **high availability** (traffic distributed across AZs, unhealthy instances replaced) and **elasticity** (capacity matches demand automatically). These are the most important architectural patterns for the CLF-C02 exam and for real cloud deployments.

---

*Kateryna | AWS Cloud Practitioner Exam Prep*
