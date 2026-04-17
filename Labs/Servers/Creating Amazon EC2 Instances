**Lab Creating Amazon EC2 Instances**

> **Course:** AWS Cloud Practitioner (CLF-C02)  
> **Topic:** EC2 Launch Methods — Console, CLI, Bastion Host Architecture  
> **Date:** <!-- add date -->

---

## Lab Objective

Launch EC2 instances using two different methods — the AWS Management Console and the AWS CLI — and build a two-tier architecture with a bastion host and a web server inside a VPC.

---

## Tasks Completed

### ✅ Task 1 — Launch a Bastion Host via AWS Console
- Launched a **t3.micro** Amazon Linux 2 instance named `Bastion host`
- Placed it in **Lab VPC > Public Subnet** with auto-assigned public IP
- Attached **Bastion-Role** IAM instance profile to allow CLI access to AWS services
- Created **Bastion security group** — SSH only, no unnecessary open ports
- No key pair used — connected via **EC2 Instance Connect**

### ✅ Task 2 — Connect via EC2 Instance Connect
- Connected to the bastion host directly from the browser — no SSH client or key file required

### ✅ Task 3 — Launch a Web Server via AWS CLI
From the bastion host terminal, ran a sequence of CLI commands to programmatically launch a second instance:

1. Retrieved the latest **Amazon Linux 2 AMI ID** from SSM Parameter Store
2. Retrieved the **Public Subnet ID** by tag name
3. Retrieved the **Web Security Group ID** by group name
4. Downloaded a **User Data script** to auto-install Apache and a web app
5. Launched the web server instance with `aws ec2 run-instances`
6. Monitored instance state with `aws ec2 describe-instances` until `running`
7. Retrieved the **public DNS name** and verified the web app loaded in a browser

---

## Screenshots

### Bastion Host — Running in EC2 Console

<img width="1920" height="1040" alt="task1-bastion-running" src="https://github.com/user-attachments/assets/bff56eeb-bfce-44ae-b78d-d89ece93d474" />

---

### EC2 Instance Connect — Terminal Session

<img width="1920" height="1040" alt="task2-instance-connect-1" src="https://github.com/user-attachments/assets/84f4abb2-2a7f-4b6d-91c7-f1ce5b5ab306" />


### CLI — Web Server Instance Launched

<img width="1920" height="1040" alt="task2-instance-connect-2" src="https://github.com/user-attachments/assets/065286b1-7f30-40e8-92a2-9526efe69e3c" />

---

### Both Instances Visible in EC2 Console

<img width="1920" height="1040" alt="task3-both-instances" src="https://github.com/user-attachments/assets/20beaed5-4792-4f17-96ef-76b74ccf303e" />

---

## Key Takeaways

| Concept | What I Learned |
|---------|----------------|
| **Bastion Host** | A jump-server pattern — one hardened public instance used to access and manage other resources securely |
| **EC2 Instance Connect** | Browser-based SSH access with no key pair needed — cleaner and more auditable than traditional SSH |
| **IAM Instance Profile** | Grants EC2 instances permission to call AWS services — required for CLI commands to work from inside an instance |
| **SSM Parameter Store** | Used to dynamically retrieve the latest AMI ID — avoids hardcoding image IDs that go stale |
| **`aws ec2 run-instances`** | Full programmatic control over instance launch — same logic as the console, but scriptable and repeatable |
| **User Data script** | Automates post-launch configuration — web server installed and configured without any manual steps |
| **Console vs CLI** | Console for quick one-off tasks; CLI for automation, scripting, and repeatable deployments |

---

## My Reflection

This lab made the difference between the console and CLI feel real rather than theoretical. Clicking through the console to launch the bastion host was straightforward — but launching the web server entirely from the command line, assembling the right parameters step by step, gave me a much deeper understanding of what the console is actually doing under the hood. Every dropdown in the UI maps to a flag in the CLI command.

The **bastion host pattern** was a new concept for me, and an important one. In production environments — especially in FinTech — you don't expose every server directly to the internet. You have one hardened entry point, and everything else sits behind it. This lab made that architecture tangible.

Using **SSM Parameter Store to retrieve the latest AMI** was a small but important detail. Hardcoding an AMI ID means your automation breaks when AWS updates the image. Fetching it dynamically is the professional approach — and it's the kind of habit that separates a one-time script from reliable infrastructure code.

The **IAM Instance Profile** was another quiet but critical piece. Without it, the CLI commands from inside the bastion host would have failed with an access denied error. It reinforced once more that in AWS, every action requires an explicit permission — nothing is assumed.

> 💡 **Key Insight:** The console and the CLI are two interfaces to the same API. Learning the CLI means understanding what AWS is actually doing — and that knowledge transfers directly into automation, scripting, and eventually Infrastructure as Code.

---

*Author: Kateryna | AWS Cloud Practitioner Exam Prep*

