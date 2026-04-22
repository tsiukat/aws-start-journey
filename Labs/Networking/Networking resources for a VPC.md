
# Lab - Creating Networking Resources in Amazon VPC

> **Course:** AWS Cloud Practitioner (CLF-C02)  
> **Topic:** VPC, IGW, Route Table, NACL, Security Group, EC2, Connectivity Test  
---

## Lab Scenario

A startup owner (Brock) has a VPC set up but cannot ping the internet. The task: build all required networking resources from scratch - VPC, subnet, internet gateway, route table, NACL, security group - and verify connectivity with `ping google.com`.

---

## Architecture Built

```
Internet
    ↓
Internet Gateway (IGW test VPC)
    ↓
Route Table (Public route table) - route: 0.0.0.0/0 → IGW
    ↓
Public Subnet - 192.168.1.0/26
    ↓
NACL (Public Subnet NACL) - allow all inbound + outbound
    ↓
Security Group (public security group) - allow SSH, HTTP, HTTPS inbound
    ↓
EC2 Instance (Amazon Linux 2023, t3.micro)
```

---

## Tasks Completed

### ✅ Task 1 - Built All VPC Networking Resources

| Resource | Name | Configuration |
|----------|------|---------------|
| VPC | `Test VPC` | CIDR: `192.168.0.0/18` |
| Subnet | `Public subnet` | CIDR: `192.168.1.0/26`, public |
| Internet Gateway | `IGW test VPC` | Attached to Test VPC |
| Route Table | `Public route table` | Route: `0.0.0.0/0 → IGW`; associated to Public subnet |
| Network ACL | `Public Subnet NACL` | Rule 100: Allow all traffic inbound + outbound |
| Security Group | `public security group` | Inbound: SSH, HTTP, HTTPS; Outbound: all |

### ✅ Task 2 - Launched EC2 Instance
- AMI: Amazon Linux 2023, type: t3.micro
- Placed in Test VPC → Public Subnet, Auto-assign Public IP: Enabled
- Security group: `public security group`
- Connected via SSH using key pair `vockey`

### ✅ Task 3 - Verified Internet Connectivity

```bash
ping google.com
```

Received replies from `google.com` with **0% packet loss** - VPC is fully routable to the internet ✅

---

## Screenshots

### Route Table - IGW Route Added and Subnet Associated

<!-- Add screenshot: Route table showing 0.0.0.0/0 → IGW and Public subnet association -->
![Route Table](screenshots/task1-route-table.png)

---

### IGW Attached to VPC

<!-- Add screenshot: Internet Gateway page showing state "Attached" to Test VPC -->
![IGW Attached](screenshots/task1-igw-attached.png)

---

### NACL - Inbound and Outbound Rules

<!-- Add screenshot: NACL inbound and outbound rules showing Rule 100: Allow All Traffic -->
![NACL Rules](screenshots/task1-nacl-rules.png)

---

### Security Group - Inbound Rules

<!-- Add screenshot: Security group inbound rules showing SSH, HTTP, HTTPS allowed -->
![Security Group](screenshots/task1-security-group.png)

---

### ping google.com - Successful

<!-- Add screenshot: terminal showing ping google.com with replies and 0% packet loss -->
![Ping Success](screenshots/task3-ping-success.png)

---

## Key Takeaways

| Concept | What I Learned |
|---------|----------------|
| **Build order matters** | VPC → Subnet → IGW → Route Table → NACL → Security Group → EC2 - each resource depends on the previous |
| **IGW route** | Always `0.0.0.0/0 → IGW` - this is what makes a subnet "public" |
| **Route table association** | A route table must be explicitly associated to a subnet - creating it is not enough |
| **NACL vs Security Group** | NACL = stateless, subnet level; Security Group = stateful, instance level - both must allow traffic |
| **NACL default behavior** | Unlike Security Groups, NACLs do not block everything by default - but a custom NACL starts with DENY ALL until rules are added |
| **Security Group** | Cannot deny specific traffic - only allow; everything not explicitly allowed is automatically blocked |
| **ping as a diagnostic** | A successful ping confirms IGW attachment, route table, NACL, and security group are all correctly configured |

---

## My Reflection

This was the most complete networking lab so far - building every component of a routable VPC from scratch, in order. What made it different from previous labs is that nothing was pre-configured. Every resource had to be created, named, connected, and verified manually.

The moment that crystallized the architecture was adding the route `0.0.0.0/0 → IGW` to the route table. Without that single line, the subnet has no path to the internet regardless of how correctly everything else is configured. That route is what makes a subnet "public" - not just the fact that it has public IPs.

The difference between NACL and Security Group also became much clearer in practice. NACL rules are numbered and stateless - you need explicit inbound AND outbound rules. Security groups are stateful - an allowed inbound connection automatically permits the return traffic. Understanding which layer is blocking traffic is a key troubleshooting skill.

Running `ping google.com` at the end and seeing replies was genuinely satisfying - not because the command is complex, but because it validated the entire stack at once.

> 💡 **Key Insight:** A VPC without a route to an Internet Gateway is completely isolated. Every component in the chain - IGW, route table, NACL, security group - must be correctly configured for traffic to flow. Miss any one of them and the result is the same: silence.

---

*Author: Kateryna | AWS Cloud Practitioner Exam Prep*
