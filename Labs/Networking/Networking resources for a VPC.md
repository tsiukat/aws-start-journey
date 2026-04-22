
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

VPC `Test VPC` CIDR: `192.168.0.0/18`
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/99d94bea-8360-4be4-bcee-f6d6066eeb12" />

Subnet -`Public subnet` CIDR: `192.168.1.0/28`, public
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/b12452ec-29ff-4a24-8f64-0eb198adcaf4" />

Internet Gateway- `IGW test VPC` Attached to Test VPC
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/1ab8eced-ef0a-4d2c-a8a3-5c1495ea5d6c" />

Route Table- `Public route table`  Route: `0.0.0.0/0 → IGW`; associated to Public subnet
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/18a858a0-8f99-438c-8065-a00dc1785594" />

Rule 100: Allow all traffic outbound
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/d1dd8260-6e18-4411-892b-c35494a09233" />

Rule 100: Allow all traffic inbound 
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/dab42b3b-14ee-42da-bde5-158f96633d48" />

Security Group - `public security group` Inbound: SSH, HTTP, HTTPS; Outbound: all 
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/5e477fc8-a1c3-4335-9ca2-a52a2f293a40" />

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/95e48297-2aca-465c-a5a2-749473e283c2" />


### ✅ Task 2 - Launched EC2 Instance
- AMI: Amazon Linux 2023, type: t3.micro
- Placed in Test VPC → Public Subnet, Auto-assign Public IP: Enabled
- Security group: `public security group`
- Connected via SSH using key pair `vockey`
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/8f050665-7b20-4459-a5b4-dced6822b88c" />
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/f4441dd9-f0be-49ed-bee0-5538205f3252" />


### ✅ Task 3 - Verified Internet Connectivity

```bash
ping google.com
```
<img width="1366" height="728" alt="image" src="https://github.com/user-attachments/assets/c80d988a-5f2f-443e-8043-d41515ea34b3" />

Received replies from `google.com` with **0% packet loss** - VPC is fully routable to the internet ✅

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
