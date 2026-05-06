# Challenge Lab: Amazon EC2 Instance — Deploy a Web Application

**Course:** AWS Cloud Practitioner Exam Prep  
**Topic:** Amazon EC2 — Independent VPC + Instance Setup + Web Server Deployment  

---

## Challenge Overview

No step-by-step guidance provided. I had to independently:
1. Design and configure a VPC with correct networking components
2. Launch an Amazon Linux EC2 instance with the right settings
3. Install and start Apache via User Data script
4. Deploy a custom HTML page and verify it in the browser

---

## Step 1 — Configure the VPC

Before launching the instance, the following networking components were created manually:

| Component | Configuration |
|---|---|
| VPC | New VPC with custom IPv4 CIDR |
| Subnet | New public subnet, auto-assign public IPv4 enabled |
| Internet Gateway | Created and attached to the VPC |
| Route Table | Route `0.0.0.0/0 → IGW` added, subnet associated |
| Security Group | Inbound: **SSH (22)** + **HTTP (80)** from `0.0.0.0/0` |

> **Screenshot:** VPC Console — VPC, subnet, IGW, and route table all configured

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/38cc4fa0-8cff-40f2-9096-a71000aa0259" />


---

## Step 2 — Launch EC2 Instance

In **EC2 Console → Launch instances**:

| Setting | Value |
|---|---|
| AMI | Amazon Linux 2 (HVM) |
| Instance type | `t3.micro` (smaller than medium) |
| VPC | New VPC created above |
| Subnet | New public subnet |
| Auto-assign public IP | Enabled |
| Storage | General Purpose SSD (gp2) |
| Security Group | SSH + HTTP allowed |

**User Data script** (installs and starts Apache on launch):

```bash
#!/bin/bash
# Update packages
yum update -y
# Install Apache web server (httpd)
yum install -y httpd
# Start httpd service and enable it to start on boot
systemctl start httpd
systemctl enable httpd
# Grant write permissions for the /var/www directory to ec2-user
usermod -a -G apache ec2-user
chown -R ec2-user:apache /var/www
chmod 2775 /var/www
find /var/www -type d -exec chmod 2775 {} \;
find /var/www -type f -exec chmod 0664 {} \;
```

> **Screenshot:** EC2 Console → instance status = **2/2 checks passed**
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/67724c09-1f7c-4722-90ef-4199dcda6c47" />


> **Screenshot:** EC2 System Log — confirming `httpd` was successfully installed and started

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/bcf9a47b-a1c5-4902-a055-652f75a1672f" />


---

## Step 3 — Deploy the Web Page

Connected to the instance via **EC2 Instance Connect** (browser-based SSH — no key file needed).

Created the HTML file:

```bash
cat <<EOF | sudo tee /var/www/html/projects.html
<!DOCTYPE html>
<html>
<body>
<h1>Kateryna's re/Start Project Work</h1>
<p>EC2 Instance Challenge Lab</p>
</body>
</html>
EOF
```

> **Screenshot:** EC2 Instance Connect terminal — file written to `/var/www/html/projects.html`

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/b6939433-2e34-4880-aaa1-6d4bf40ccff9" />


---

## Step 4 — Verify in Browser

Opened in browser:
```
http://35.85.151.243/projects.html
```

> **Screenshot:** Browser — custom HTML page loaded successfully ✅

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/6c599cf0-962e-424b-8867-c386ede5c291" />


---

## What Had to Work Together

```
Internet
    │
Internet Gateway
    │
Public Subnet (auto-assign IPv4)
    │
EC2 Instance (t3.micro, Amazon Linux 2)
    │
Security Group: SSH (22) + HTTP (80)
    │
Apache httpd (installed via User Data)
    │
/var/www/html/projects.html
```

Any single missing piece — no IGW, wrong route, missing SG rule, httpd not started — would break the chain.

---

## Reflection

In this challenge lab I learned how to:

- **Build the full EC2 deployment independently** — from VPC and IGW through to a running web page, without step-by-step prompts; this required mentally sequencing all dependencies before starting
- **Use User Data to automate server setup at launch** — the `httpd` install and start runs once at boot, which means the instance is production-ready the moment it passes status checks
- **Use `chmod 777 /var/www/html`** to grant write access to the document root, and `sudo tee` to write files to a protected directory from a non-root session
- **Validate success at each layer** — system log confirmed httpd install, status checks confirmed network reachability, browser confirmed the full stack worked end to end

> Key takeaway: A challenge lab tests whether knowledge transfers to independent decision-making. Knowing *which* components to create and *in what order* (VPC → IGW → route table → subnet → SG → EC2) is the real skill — the Console just executes the decisions.

---

*Kateryna | AWS Cloud Practitioner Exam Prep*
