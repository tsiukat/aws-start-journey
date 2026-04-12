# Lab Project — 3D E-Commerce Platform Architecture on AWS

**AWS Cloud Practitioner Program · Architecture Design Assignment**

---

## What This Project Is

This lab documents the cloud architecture design for a next-generation **3D e-commerce platform** — a web application where users can interact with 3D models of products (furniture, gadgets, fashion items) before purchasing.

The platform is expected to serve **millions of users globally** and must meet five key requirements: high availability, scalability, performance, security, and cost optimization.

> This is an architecture design project. The deliverable is a diagram and a written rationale — not code.

---

## Architecture Diagram

![3D E-Commerce AWS Architecture](./screenshots/3D_E-Commerce_Platform.png)

---

## How the Architecture Works — Flow from User to Data

The diagram is read **top to bottom**, following the path of a real user request.

### 1. Users → Internet → Route 53

A user opens the browser and types the site address. **Route 53** (AWS DNS service) translates the domain name into a server address and routes the user to the nearest AWS region. This is always the first element in any AWS architecture — without DNS, the browser has no idea where to go.

### 2. Route 53 → CloudFront → S3

**CloudFront** is a Content Delivery Network with 400+ edge locations worldwide. 3D model files are large — tens of megabytes. Instead of downloading them from one central server, CloudFront caches them at the edge location closest to the user (e.g. Frankfurt for Warsaw). **S3** stores the original 3D models, product images, and static files. CloudFront fetches from S3 and serves cached copies globally. This directly addresses the **Performance** requirement.

### 3. CloudFront → AWS Shield + WAF

All traffic passing through CloudFront is protected by two security layers:

- **AWS Shield** — protects against DDoS attacks (floods of fake traffic designed to crash the system)
- **WAF (Web Application Firewall)** — filters malicious requests before they reach the servers

These sit at the edge — threats are blocked before they ever enter the internal network.

### 4. Route 53 → ELB (one per VPC)

Dynamic user actions — login, cart, checkout — go to the **Elastic Load Balancer**. The ELB distributes incoming requests evenly across EC2 instances so no single server gets overloaded. Two ELBs are deployed, one per VPC, supporting the multi-region design described below.

### 5. ELB → VPC → EC2 + Auto Scaling

The architecture uses **two separate VPCs**, each deployed in its own AWS Region.

**Why two VPCs and two ELBs?**
This design provides **geographic redundancy** — if an entire AWS region goes down, the second VPC in a different region continues serving users without interruption. This is a stronger form of resilience than a single-region Multi-AZ setup and directly satisfies the **High Availability** requirement at regional scale. Each VPC has its own ELB because each region operates as an independent, fully functional unit.

Inside each VPC:

- **Public subnet** — EC2 application servers with Auto Scaling. When traffic spikes, Auto Scaling launches additional instances automatically. When it drops, they shut down. This satisfies **Scalability** and **Cost Optimization**.
- **Two Availability Zones** inside each VPC — an extra layer of redundancy within the region itself.

### 6. EC2 → Aurora + DynamoDB (Private Subnets)

Application servers write and read from two databases, both in **private subnets** — not reachable from the internet:

- **Amazon Aurora (RDS)** — relational database for transactional data: customer accounts, orders, payment records. Chosen because orders require strict consistency — money cannot be charged without a confirmed order record.
- **Amazon DynamoDB** — NoSQL database for the product catalog and user sessions. Delivers single-digit millisecond reads at any scale — essential when millions of users browse products simultaneously.

Both databases are replicated across Availability Zones within each VPC.

### 7. EC2 → Lambda

**AWS Lambda** handles lightweight event-driven tasks: sending order confirmation emails, updating stock counts, notifying the warehouse. Lambda runs only when triggered and costs nothing when idle — a direct contribution to **Cost Optimization**.

### 8. Security — AWS IAM + KMS

Applied across the entire architecture:

- **AWS IAM** — every service has a role with minimum required permissions. EC2 cannot access S3 arbitrarily. Lambda cannot read the database directly. Least-privilege access is enforced throughout.
- **AWS Key Management Service (KMS)** — manages encryption keys for data at rest across S3, RDS, and DynamoDB.

### 9. Monitoring — CloudWatch + Trusted Advisor

- **Amazon CloudWatch** — monitors CPU usage, error rates, database response times, and Lambda execution across both VPCs. Triggers alarms when metrics exceed thresholds.
- **AWS Trusted Advisor** — runs daily checks for cost inefficiencies, idle resources, and security gaps. Ensures the platform stays lean and secure over time.

---

## How the Architecture Meets Each Requirement

| Requirement | How it is addressed |
|---|---|
| **High Availability** | Two separate VPCs in two AWS Regions. If one region fails, the second continues. Two Availability Zones per VPC add a second redundancy layer. |
| **Scalability** | EC2 Auto Scaling in both VPCs. DynamoDB scales automatically. No manual action needed during traffic spikes. |
| **Performance** | CloudFront serves 3D assets from the edge location nearest to each user. DynamoDB delivers millisecond reads for the product catalog. |
| **Security** | WAF and Shield at the edge. Private subnets for all databases. IAM least-privilege roles. KMS encryption at rest. HTTPS via CloudFront. |
| **Cost Optimization** | Auto Scaling prevents over-provisioning. Lambda eliminates idle servers for background tasks. Trusted Advisor identifies unused resources. |

---

## Design Decisions and Trade-offs

**Two VPCs instead of one Multi-AZ VPC**
The architecture uses two fully separate VPCs across two AWS Regions rather than a single VPC with two Availability Zones. This provides regional-level fault tolerance — protection against a full region outage, not just a single data center failure. The trade-off is higher operational complexity and cost. For a global platform serving millions of users, this is a justified design choice.

**Two databases instead of one**
Aurora handles transactional data where accuracy is critical. DynamoDB handles high-volume reads where speed matters more than relational structure. Using both means each database does exactly what it is built for.

**Lambda alongside EC2**
EC2 runs the core application logic continuously. Lambda handles infrequent event-triggered tasks. Running those tasks on EC2 would mean paying for a server that sits idle most of the time.

---

## Screenshot

> To add your diagram: in draw.io go to File → Export as → PNG → name the file `3D_E-Commerce_Platform.png` → upload it to the `screenshots/` folder in this repository.

---

## My Reflection — What I Learned

Before this project, AWS felt like a long and disconnected list of service names. Designing this architecture from scratch changed that completely.

The most important thing I learned is that **architecture is not a list of services — it is the story of a user's journey**. Once I traced what actually happens when someone opens a website and clicks on a 3D model, every service stopped being an abstract name and became a logical answer to a specific problem. CloudFront exists because 3D files are heavy and users are everywhere. Two Availability Zones exist because a single data center can lose power. Lambda exists because not every task needs a server running around the clock.

I also learned the difference between **availability and scalability** — two words I used interchangeably before. Availability is about staying online when something breaks. Scalability is about handling more load when more people arrive. They are solved by different services and different design decisions.

Designing the security layer taught me that **security is not one decision — it is many layers**. WAF blocks bad traffic at the edge. Private subnets hide the servers from the internet. IAM limits what each service can do internally. KMS encrypts the data itself. Each layer catches what the previous one might miss.

Finally, I understood that **cost optimization is a design decision, not something you fix after launch**. Choosing Lambda over EC2 for background tasks, using Auto Scaling instead of fixed capacity, storing assets in S3 — these choices directly affect the monthly bill and are made at the architecture stage, before a single server is turned on.

This was my first time thinking like an architect rather than a user. I now look at every website differently.

---

## Tools Used

- [draw.io](https://app.diagrams.net/) — architecture diagram with built-in AWS icon library

## Program

AWS Cloud Practitioner preparation · Bring Women Back to Work · Salesforce Switzerland
