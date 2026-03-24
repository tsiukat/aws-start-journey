
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

### Script Running — Instance Launched Successfully

<!-- Add screenshot: terminal output after script completes with public IP assigned -->
![Script Success](screenshots/task3-script-success.png)

---

### Issue #1 — AMI Not Found Error

<!-- Add screenshot: terminal error "InvalidAMIID.NotFound" -->
![AMI Error](screenshots/task3-issue1-ami-error.png)

---

### Issue #2 — nmap Scan Showing Port 80 Closed

<!-- Add screenshot: nmap output before security group fix -->
![nmap Scan](screenshots/task3-issue2-nmap.png)

---

### Security Group Fixed — Port 80 Open

<!-- Add screenshot: inbound rules updated with HTTP port 80 -->
![SG Fixed](screenshots/task3-issue2-sg-fixed.png)

---

### Café Web Application Live

<!-- Add screenshot: Café home page loaded in browser -->
![Cafe App](screenshots/task4-cafe-home.png)

---

### Order Placed and History Confirmed

<!-- Add screenshot: Order History page showing submitted orders -->
![Order History](screenshots/task4-order-history.png)

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
