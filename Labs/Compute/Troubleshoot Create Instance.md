
# Lab 04 — Launching a LAMP Stack EC2 Instance via AWS CLI

> **Course:** AWS Cloud Practitioner (CLF-C02)  
> **Topic:** EC2 CLI Launch, LAMP Stack, Troubleshooting  
> **Date:** <!-- add date -->
 
---

## Lab Objective

Use the AWS CLI to launch an EC2 instance with a full LAMP stack (Linux, Apache, MariaDB, PHP) running the Café Web Application — and troubleshoot two real configuration errors along the way.

---

## Tasks Completed

### ✅ Task 1 & 2 — Connect to CLI Host and Configure AWS CLI
- Connected to the pre-provisioned **CLI Host** instance via EC2 Instance Connect
- Ran `aws configure` with lab credentials and region

### ✅ Task 3 — Run and Troubleshoot the Launch Script

Analyzed `create-lamp-instance-v2.sh` — a shell script that automatically finds the correct VPC, subnet, AMI, and security group, then launches a `t3.small` EC2 instance with a User Data script that installs Apache, MariaDB, and PHP.

The script contained **two intentional bugs** that needed to be identified and fixed:

---

**🐛 Issue #1 — InvalidAMIID.NotFound**

The `run-instances` command failed because the AMI ID was being looked up in the wrong AWS Region. The script retrieved a valid AMI for a different region, which does not exist in the deployment region.

**Fix:** Corrected the Region reference in the script so the AMI lookup and instance launch used the same region consistently.

---

**🐛 Issue #2 — Website Not Accessible (port 80 blocked)**

The instance launched successfully and received a public IP, but the web app did not load. Used `nmap` to scan open ports — port 80 was not reachable.

**Fix:** Updated the security group to allow inbound **HTTP traffic on port 80**. After the fix, the web server responded correctly.

---

### ✅ Task 4 — Verify the Café Web Application
- Opened `http://<public-ip>/cafe` — Café home page loaded successfully
- Placed orders via the **Menu** page and confirmed they appeared in **Order History**
- Verified that order data was being stored in the **MariaDB database** running on the same instance

---

## Screenshots

### Script Running — Instance Launched Successfully & AMI Not Found Error

<img width="1920" height="1040" alt="task4 Instance Launched  Error AMIID" src="https://github.com/user-attachments/assets/54bc2a98-73a8-4e2e-bdb1-9e0df2744cb6" />


---


### Region Change

<img width="1920" height="1040" alt="task4 error scrypt" src="https://github.com/user-attachments/assets/1a1093ec-2de8-408f-b701-c08e852d8c73" />
<img width="1920" height="1040" alt="task4 error scrypt - added" src="https://github.com/user-attachments/assets/9529f7e7-796d-4f17-bd4d-e4c3ed23ba7b" />

---

### Order Placed and History Confirmed

<img width="1920" height="1040" alt="task4 order confirmation" src="https://github.com/user-attachments/assets/569a24ef-458b-4ff1-9ae8-d9168d7a53c1" />
---

## Key Takeaways

| Concept | What I Learned |
|---------|----------------|
| **LAMP Stack** | Linux + Apache + MariaDB + PHP — a classic single-instance web app architecture, fully automated via User Data |
| **AMI Region Dependency** | AMI IDs are region-specific — using an AMI ID from the wrong region causes `InvalidAMIID.NotFound` |
| **Security Group as Firewall** | A running instance with a blocked port 80 is invisible to the web — even if the server process is running |
| **nmap for Troubleshooting** | Port scanning confirms what is actually reachable — a faster diagnostic than guessing |
| **User Data + cloud-init** | The `/var/log/cloud-init-output.log` file logs every command from the User Data script — essential for debugging |
| **Shell Script Automation** | A single script can find VPC, subnet, AMI, create a security group, and launch an instance — with no manual input |

---

## My Reflection

This lab was different from the previous ones — instead of following clean steps, I had to **find and fix real errors**. That changed how I engaged with the material entirely. Debugging the AMI region mismatch taught me something that no diagram can: AWS resources are region-scoped, and one wrong reference silently breaks everything downstream.

The `nmap` scan for Issue #2 was a practical troubleshooting tool I had not used before in a cloud context. Seeing port 80 absent from the results made the security group's role completely concrete — it is not just a configuration checkbox, it is an actual network barrier.

Watching the Café app work end-to-end — placing orders, seeing them saved in the database — made the LAMP stack feel real rather than theoretical. One EC2 instance, one User Data script, one working web application with a database backend.

> 💡 **Key Insight:** Troubleshooting is a skill, not a failure state. Knowing *where* to look — the script, the region, the security group, the cloud-init log — is as important as knowing how to launch an instance correctly in the first place.

---

*Author: Kateryna | AWS Cloud Practitioner Exam Prep*
