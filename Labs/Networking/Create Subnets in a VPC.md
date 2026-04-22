# Lab — Create Subnets and Allocate IP Addresses in Amazon VPC

> **Course:** AWS Cloud Practitioner (CLF-C02)  
> **Topic:** VPC, CIDR, Subnets, RFC 1918, IP Address Planning  

---

## Lab Scenario

A startup owner (Paulo) needs help setting up his first VPC. Requirements:
- ~15,000 private IP addresses in the VPC
- IPv4 CIDR in the `192.x.x.x` range
- At least 50 IP addresses in a public subnet

---

## CIDR Planning

| Requirement | Solution | Reasoning |
|-------------|----------|-----------|
| ~15,000 IPs for VPC | `192.168.0.0/18` | /18 = 16,382 usable IPs — closest range above 15,000 |
| 192.x.x.x private range | `192.168.0.0/16` block (RFC 1918) | Confirmed private range: `192.168.0.0 – 192.168.255.255` |
| At least 50 IPs for public subnet | `192.168.1.0/26` | /26 = 64 IPs (62 usable) — smallest block above 50 |

> **RFC 1918 private ranges:**  
> `10.0.0.0/8` · `172.16.0.0/12` · `192.168.0.0/16`

---

## Tasks Completed

### ✅ Task 1 — Created the VPC

Used **VPC Wizard → VPC with a Single Public Subnet** and configured:

- **VPC name:** `First VPC`
- **IPv4 CIDR block:** `192.168.0.0/18` (~16,382 IPs)
- **Public subnet CIDR:** `192.168.1.0/26` (64 IPs)
- **Availability Zone:** No Preference
- **Subnet name:** `Public subnet`

Verified: VPC appeared in **Your VPCs** with status Available. Internet Gateway and Route Table created automatically by the wizard.

---
## Key Takeaways

| Concept | What I Learned |
|---------|----------------|
| **RFC 1918** | Three reserved private IP ranges — `10.x`, `172.16–31.x`, `192.168.x` — not routable on the public internet |
| **CIDR /18** | Provides 16,382 usable IPs — right-sizing a VPC means choosing the smallest block that still fits your needs |
| **CIDR /26** | Provides 64 IPs (62 usable) — enough for a small public subnet with room to grow |
| **Public vs Private Subnet** | Public subnet = instances reachable from internet via IGW; private subnet = no direct internet access |
| **VPC Wizard** | Creates VPC, public subnet, internet gateway, and route table in one step — good for standard single-subnet setups |
| **IP address planning** | Must be done before creating a VPC — CIDR blocks cannot overlap and are difficult to change after launch |

---

## My Reflection

This lab moved from working inside existing infrastructure to **designing infrastructure from scratch** — and that required a different kind of thinking. Instead of following steps, I had to translate a customer's requirement ("~15,000 IPs") into a specific CIDR notation, which meant understanding what /18 actually means in terms of host count.

Using the subnet calculator made CIDR concrete rather than abstract. Seeing that /18 gives 16,382 hosts and /26 gives 64 — and understanding why those numbers come from the binary math of the subnet mask — turned notation into something meaningful.

The question about why private IPs are used inside a VPC was also clarifying: private ranges are non-routable on the public internet by design. Resources inside the VPC talk to each other privately, and only traffic that explicitly needs to leave the VPC goes through the internet gateway. That separation is the foundation of VPC security.

> 💡 **Key Insight:** IP address planning is one of the first and most consequential decisions in cloud architecture. A VPC CIDR that is too small limits future growth; one that is too large wastes address space. Getting it right means understanding both your current needs and your likely growth — before you write a single line of configuration.

---

*Author: Kateryna | AWS Cloud Practitioner Exam Prep*
