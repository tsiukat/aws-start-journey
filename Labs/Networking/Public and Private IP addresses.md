# Lab Internet Protocols: Public and Private IP Addresses

> **Course:** AWS Cloud Practitioner (CLF-C02)  
> **Topic:** VPC Networking, Public vs Private IP, CIDR, Troubleshooting  

---

## Lab Scenario

A cloud admin (Jess) reports that two EC2 instances in the same VPC and subnet have identical AWS configurations — but only one can reach the internet. The task: diagnose the root cause and advise on a second question about using a public CIDR range for a new VPC.

---

## Tasks Completed

### ✅ Task 1 — Investigated the Customer's Environment

Compared the Networking tab of both EC2 instances:

| | Instance A | Instance B |
|---|---|---|
| Private IPv4 | ✅ assigned | ✅ assigned |
| Public IPv4 | ❌ not assigned | ✅ assigned |
| Internet access | ❌ no | ✅ yes |
<img width="1920" height="1040" alt="Instance B - yes public" src="https://github.com/user-attachments/assets/12da5db5-e90b-4832-9cd3-da529264b299" />
<img width="1920" height="1040" alt="No public" src="https://github.com/user-attachments/assets/66e7a8e9-da3a-42c8-9070-aeb751c68d3d" />


**Root cause:** Instance A has no public IP address. Private IPs are only routable within the VPC — without a public IP, the instance cannot be reached from or communicate with the internet, even if all other configurations are identical.

### ✅ Task 2 — Verified via SSH Connection

- Connected via SSH to **Instance B** (public IP) — ✅ successful
- This confirmed the diagnosis: private IPs are not reachable from outside the VPC
<img width="1366" height="728" alt="putty connection" src="https://github.com/user-attachments/assets/89d24c5b-3571-48b9-993f-98f2b617b61f" />

**Customer's second question — Using a public CIDR (e.g. 12.0.0.0/16) for a VPC:**

Not recommended. AWS VPCs should use **RFC 1918 private ranges** (`10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`). Using a public IP range for a VPC CIDR can cause routing conflicts — if resources inside the VPC try to reach the real public owner of that IP range, responses may be misrouted back into the VPC instead of reaching the correct destination.
<img width="1920" height="1040" alt="Whois IP 12 0 0 0" src="https://github.com/user-attachments/assets/19a8e04a-7b53-4db4-b1ce-8880d528d09b" />

## Key Takeaways

| Concept | What I Learned |
|---------|----------------|
| **Private IP** | Routable only within the VPC — cannot initiate or receive connections from the internet |
| **Public IP** | Required for direct internet communication — assigned at launch or via Elastic IP |
| **Same config ≠ same behavior** | Two instances with identical VPC settings behave differently based solely on whether a public IP is assigned |
| **RFC 1918 private ranges** | VPCs should use `10.x`, `172.16–31.x`, or `192.168.x` — these are reserved for private use and will not conflict with real internet routing |
| **Public CIDR in VPC** | Using a real public IP range (e.g. `12.0.0.0/16`) for a VPC CIDR can cause routing conflicts with the actual owners of that IP space |
| **OSI model in AWS** | EC2 = Layer 5 (session), Security Groups/NACLs = Layer 4 (transport), Route tables/IGW = Layer 3 (network) — useful mental model for troubleshooting |

---

## My Reflection

This lab was less about clicking through a console and more about **reading network behavior** — which is a different kind of skill. Two instances, same VPC, same subnet, same security group, but completely different internet behavior. The only difference was one IP address field.

That made the concept of public vs private IP feel genuinely important, not just definitional. In real support scenarios, this kind of subtle difference — something that does not trigger an error, just a failed connection — is exactly what makes networking troubleshooting hard.

The question about using a public CIDR for a VPC was also valuable. It sounds like it should work — it is just a number range — but the consequence (routing replies to the wrong destination) is the kind of subtle, hard-to-debug issue that only surfaces under specific traffic conditions. That is why private ranges exist: they are guaranteed never to be routed on the public internet.

> 💡 **Key Insight:** In cloud networking, the absence of a public IP is not an error — it is a design choice. But if that choice was accidental, the instance simply goes silent: no error message, no alert, just no connectivity. Understanding IP addressing is what makes the silence meaningful.

---

*Author: Kateryna | AWS Cloud Practitioner Exam Prep*
