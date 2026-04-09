
# ☁️ AWS Cloud Architecture — 3D E-Commerce Platform

**AWS Cloud Practitioner Study Project · Team Assignment**  
Designed as part of the AWS re/Start / Cloud Practitioner preparation program.

---

## 📌 Project Overview

This project documents the design of a cloud architecture on AWS for a next-generation **3D e-commerce platform** — a web application where users can interact with 3D models of products (furniture, gadgets, fashion) before purchasing.

The platform is expected to serve **millions of users globally** and must be fast, always available, secure, and cost-efficient.

> **This is an architecture design project** — no code was written. The deliverable is a architecture diagram and a written explanation of service choices.

---

## 🎯 Business Requirements

| Requirement | Description |
|---|---|
| **High Availability** | Platform available 24/7 with fault tolerance and automatic failover |
| **Scalability** | Handles unpredictable traffic spikes without manual intervention |
| **Performance** | Fast 3D content delivery, quick page loads, smooth rendering |
| **Security** | AWS security best practices: IAM, VPC, encryption, HTTPS |
| **Cost Optimization** | No over-provisioning; pay-as-you-go where possible |

---

## 🏗️ Architecture Diagram

> **Screenshot placeholder — paste your diagram here**

![AWS Architecture Diagram](./screenshots/architecture-diagram.png)

*The diagram shows the full request flow: Users → Route 53 → CloudFront → ALB → EC2 (Multi-AZ) → RDS / DynamoDB, with S3 as asset storage and CloudWatch + Trusted Advisor for monitoring.*

---

## 🧩 AWS Services Used

### 🌐 Networking & Routing

**Route 53**  
DNS service. Translates the domain name into the correct server address. Configured with latency-based routing so users are automatically directed to the nearest AWS region.

**Amazon CloudFront (CDN)**  
Content Delivery Network. Caches and delivers 3D model files (GLB/GLTF), textures, and static assets from 400+ edge locations worldwide. This is the most impactful service for performance — users receive assets from the location closest to them, not from a central server.

### 📦 Storage

**Amazon S3**  
Object storage for all static assets: 3D models, product images, and front-end files. Integrated with CloudFront as the origin. S3 Lifecycle policies automatically move older assets to cheaper storage tiers (S3-IA → Glacier) to control costs.

### ⚖️ Traffic Distribution

**Elastic Load Balancer (ALB)**  
Distributes incoming HTTPS traffic evenly across EC2 instances. Performs health checks — if a server goes down, traffic is automatically rerouted. The only entry point from the internet into the VPC.

### 💻 Compute

**Amazon EC2 with Auto Scaling**  
Application servers running in two Availability Zones (Multi-AZ). Auto Scaling launches new instances when CPU usage is high and terminates them when traffic drops — ensuring performance under load without over-provisioning.

**AWS Lambda**  
Serverless compute for event-driven tasks: order confirmation emails, thumbnail generation for new 3D uploads, background notifications. Billed per execution — no idle cost.

### 🗄️ Databases

**Amazon RDS Aurora (Multi-AZ)**  
Relational database for structured transactional data: customer accounts, orders, payment records. Multi-AZ deployment means automatic failover to a standby replica if the primary database fails.

**Amazon DynamoDB**  
NoSQL database for the product catalog and user sessions. Delivers single-digit millisecond read performance at any scale — essential for a platform with millions of concurrent browsers.

### 🔐 Security

**AWS IAM**  
Identity and Access Management. Every service has a role with least-privilege permissions — EC2 can only access what it needs, Lambda only what it needs. No shared credentials.

**VPC with Public and Private Subnets**  
EC2 instances and RDS databases live in private subnets with no direct internet access. Only the ALB sits in the public subnet. Security Groups act as firewalls between layers.

**ACM (SSL/TLS Certificates)**  
HTTPS enforced at CloudFront and ALB. All data in transit is encrypted.

### 📊 Monitoring & Cost Control

**Amazon CloudWatch**  
Collects metrics, logs, and alarms across all services. Alerts trigger automatically on CPU spikes, error rate increases, or database connection limits.

**AWS Trusted Advisor**  
Continuously scans the account for cost inefficiencies, security gaps (open ports, unused IAM keys), and performance risks. Provides actionable recommendations without manual audits.

---

## 📐 Architecture Decisions & Trade-offs

| Decision | Choice | Why |
|---|---|---|
| Compute layer | EC2 + Auto Scaling | More control than fully serverless; suitable for persistent app logic |
| Background tasks | Lambda | Serverless = no idle cost for infrequent jobs |
| Relational data | RDS Aurora Multi-AZ | ACID compliance for orders and payments |
| Product catalog | DynamoDB | High-speed reads at scale; no joins needed |
| Asset delivery | S3 + CloudFront | Large 3D files need edge caching; S3 alone would be slow globally |
| Network isolation | VPC with private subnets | Databases and app servers not exposed to the internet |

---

## 📸 Screenshots

> Replace placeholders with your actual screenshots from the assignment.

### Architecture Diagram (draw.io / Lucidchart)

![Architecture Diagram](./screenshots/architecture-diagram.png)

---

## 📄 Written Explanation Document

The full 2-page written explanation (service choices, how requirements are met, trade-offs) is available in this repository:

📎 [`AWS_Architecture_Explanation.docx`](./AWS_Architecture_Explanation.docx)

---

## 💡 My Reflection — What I Learned

Working on this project gave me a much clearer picture of how cloud infrastructure actually fits together. Before this, AWS felt like a long list of service names. After designing this architecture, I started to see the *logic* behind the choices — why you don't just pick one database, why two Availability Zones matter, why CloudFront is not optional for global users.

A few specific things that clicked for me:

**The difference between availability and scalability.** I used to think they were the same thing. Now I understand: availability is about staying online when something breaks (Multi-AZ, failover), while scalability is about handling more traffic when it arrives (Auto Scaling, DynamoDB). You need both, but they solve different problems.

**Why "serverless" doesn't mean "no servers."** Lambda still runs on servers — I just don't manage them. The insight is in the billing model: I pay only when the function actually runs. For tasks that happen 50 times a day (like sending an email), running a dedicated EC2 server for that would be wasteful.

**Security is not one thing — it's layers.** I learned to think in layers: network layer (VPC, subnets, security groups), identity layer (IAM roles), encryption layer (HTTPS, SSE), and monitoring layer (CloudWatch). Each layer catches what the previous one might miss.

**Cost optimization is a design decision, not an afterthought.** Choosing Lambda over EC2 for some tasks, using S3 Lifecycle policies, relying on Auto Scaling instead of running peak-capacity servers constantly — these decisions save money by design, not by accident.

This project was my first time thinking like an architect rather than a user. It changed how I see every website I visit — I now wonder: what's behind it?

---

## 👥 Team

5-person team project · AWS Cloud Practitioner Program

---

## 📚 Resources

- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)
- [AWS Architecture Center](https://aws.amazon.com/architecture/)
- [AWS Free Tier](https://aws.amazon.com/free/)
- [draw.io (diagram tool)](https://app.diagrams.net/)
