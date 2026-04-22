# Lab Internet Protocols: Static and Dynamic IP Addresses (Elastic IP)

> **Course:** AWS Cloud Practitioner (CLF-C02)  
> **Topic:** EC2 Public IP, Elastic IP (EIP), Static vs Dynamic Addressing  
---

## Lab Scenario

A customer (Bob) reports that his EC2 instance gets a new public IP address every time it is stopped and restarted — breaking all dependent resources. The task: replicate the issue, diagnose the root cause, and apply a permanent solution using an Elastic IP.

---

## Tasks Completed

### ✅ Task 1 — Replicated and Resolved the Issue

**Step 1 — Launched a test EC2 instance**
- AMI: Amazon Linux 2, type: t3.micro
- Placed in Lab VPC → Public Subnet 1, Auto-assign Public IP: **Enabled**
- Attached existing security group: `Linux Instance SG`
<img width="1920" height="1040" alt="test instance" src="https://github.com/user-attachments/assets/0503ca31-f9f8-452e-9b46-200959e07a48" />

**Step 2 — Observed dynamic IP behavior**

| Event | Public IPv4 | Private IPv4 |
|-------|-------------|--------------|
| After launch | 35.85.40.254  | 10.0.10.235 |
<img width="1920" height="1040" alt="test instance - ipv4" src="https://github.com/user-attachments/assets/ffd887fa-490b-4851-929e-c0ccaa59c6ed" />
-
| Event | Public IPv4 | Private IPv4 |
|-------|-------------|--------------|
| After Stop | ❌ released | ✅ unchanged |
<img width="1920" height="1040" alt="test instance - ipv4-after stop" src="https://github.com/user-attachments/assets/65799430-ce13-4e09-b4b9-0add505ab1c5" />
-
| Event | Public IPv4 | Private IPv4 |
|-------|-------------|--------------|
| After Start | 🔄 new IP assigned | ✅ unchanged |
<img width="1920" height="1040" alt="test instance - ipv4-after restart" src="https://github.com/user-attachments/assets/5e68e588-d63b-4896-afcf-d1e566748f99" />


**Conclusion:** The public IP is **dynamic** — released on stop and re-assigned randomly on start. The private IP is **static** within the VPC — it never changes.

**Step 3 — Allocated and associated an Elastic IP**
- EC2 Console → **Network & Security → Elastic IPs → Allocate Elastic IP address**
- Selected the new EIP → **Actions → Associate Elastic IP address**
- Associated to: `test instance`, Private IP auto-selected
- Verified: EIP now shows as the instance's Public IPv4

**Step 4 — Verified static behavior**
- Stopped and restarted the instance
- Public IPv4 remained the **same EIP address** after restart ✅
- Issue resolved — Bob's instance now has a persistent public IP

---

## Screenshots

### Before Fix — Public IP Changes After Stop/Start

<!-- Add screenshot: Networking tab showing different public IP after instance restart -->
![Dynamic IP](screenshots/task1-dynamic-ip-change.png)

---

### Elastic IP Allocated

<!-- Add screenshot: Elastic IPs page showing newly allocated EIP address -->
![EIP Allocated](screenshots/task1-eip-allocated.png)

---

### EIP Associated to Instance

<!-- Add screenshot: Associate Elastic IP page with test instance selected -->
![EIP Associated](screenshots/task1-eip-associated.png)

---

### After Fix — Public IP Stays Same After Restart

<!-- Add screenshot: Networking tab showing EIP address unchanged after stop/start -->
![Static IP](screenshots/task1-static-ip-confirmed.png)

---

## Key Takeaways

| Concept | What I Learned |
|---------|----------------|
| **Dynamic Public IP** | Assigned automatically at launch from AWS's pool — released when instance stops, re-assigned randomly on restart |
| **Private IP** | Assigned from the VPC CIDR range — stays fixed for the lifetime of the instance regardless of start/stop cycles |
| **Elastic IP (EIP)** | A static public IPv4 address that persists independently of the instance state — survives stop/start cycles |
| **EIP Association** | An EIP must be explicitly attached to an instance — once attached, it replaces the dynamic public IP |
| **EIP Cost** | An EIP is free while associated to a running instance — charges apply if allocated but not associated |
| **Real-world impact** | DNS records, firewall rules, API integrations, and SSL certificates all break when a public IP changes unexpectedly |

---

## My Reflection

This lab made the distinction between static and dynamic addressing feel real through a concrete failure scenario. Bob's problem — resources breaking every time the instance restarts — is a common and entirely preventable issue. The root cause was not a misconfiguration; it was a misunderstanding of how AWS public IPs work by default.

The most important insight was the **asymmetry between public and private IPs**: private IPs are stable within the VPC, while public IPs are ephemeral by design unless you explicitly request persistence through an Elastic IP. That default behavior exists for a reason — it allows AWS to manage its IP pool efficiently — but it has real consequences for anyone building infrastructure that depends on a fixed address.

The EIP solution is straightforward, but it comes with a responsibility: an EIP that is allocated but not associated costs money. That is a small but meaningful detail — cloud resources you forget to clean up are cloud costs you did not plan for.

> 💡 **Key Insight:** In AWS, a public IP and a static public IP are two different things. Knowing that distinction — and knowing when you need an Elastic IP versus when a dynamic address is fine — is a fundamental networking decision that affects reliability, cost, and architecture.

---

*Author: Kateryna | AWS Cloud Practitioner Exam Prep*
