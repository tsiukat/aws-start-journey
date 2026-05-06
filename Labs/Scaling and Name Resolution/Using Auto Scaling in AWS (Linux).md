# Lab: Using Auto Scaling in AWS (Linux) — CLI-Driven

**Course:** AWS Cloud Practitioner Exam Prep  
**Topic:** EC2 Auto Scaling + ALB — Deployed via AWS CLI + Console  
**Duration:** ~45 min

---

## Overview

Built the same ELB + Auto Scaling architecture as the previous lab — but this time EC2 instance creation and AMI generation were done entirely via **AWS CLI commands**, not the Console.

**Architecture — before and after:**

```
BEFORE:
[Command Host EC2] — used to run CLI commands

AFTER:
[Internet] → [Application Load Balancer: WebServerELB]
                    ↙                      ↘
      [WebApp EC2 - Private           [WebApp EC2 - Private
       Subnet 1 / AZ1]                 Subnet 2 / AZ2]
      (Auto Scaling Group: min 2, desired 2, max 4)
```

---

## Task 1 — Create EC2 Instance and AMI via AWS CLI

### Connect to Command Host and configure CLI

```bash
# Check current region
curl http://169.254.169.254/latest/dynamic/instance-identity/document | grep region

# Configure credentials
aws configure
# → Access Key ID: (press Enter)
# → Secret Access Key: (press Enter)
# → Default region: us-west-2
# → Output format: json
```

### Launch new EC2 instance (WebServer)

```bash
aws ec2 run-instances \
  --key-name KEYNAME \
  --instance-type t3.micro \
  --image-id AMIID \
  --user-data file:///home/ec2-user/UserData.txt \
  --security-group-ids HTTPACCESS \
  --subnet-id SUBNETID \
  --associate-public-ip-address \
  --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=WebServer}]' \
  --output text \
  --query 'Instances[*].InstanceId'
```

Returns: `NEW-INSTANCE-ID`

### Wait for instance to be running

```bash
aws ec2 wait instance-running --instance-ids NEW-INSTANCE-ID
```

### Get public DNS and verify web server

```bash
aws ec2 describe-instances \
  --instance-id NEW-INSTANCE-ID \
  --query 'Reservations[0].Instances[0].NetworkInterfaces[0].Association.PublicDnsName'
```

Opened `http://PUBLIC-DNS-ADDRESS/index.php` in browser — web app confirmed running.

> **Screenshot:** Terminal — `run-instances` output with InstanceId

![cli-instance-launched](screenshots/01-cli-instance-launched.png)

> **Screenshot:** Browser — WebServer `index.php` loaded successfully

![webserver-running](screenshots/02-webserver-running.png)

### Create AMI from the running instance

```bash
aws ec2 create-image --name WebServerAMI --instance-id NEW-INSTANCE-ID
```

> **Screenshot:** EC2 Console → AMIs — `WebServerAMI` status = **Available**

![ami-available](screenshots/03-ami-available.png)

---

## Task 2 — Create Load Balancer, Launch Template, Auto Scaling Group

### Application Load Balancer

| Setting | Value |
|---|---|
| Name | `WebServerELB` |
| VPC | Lab VPC |
| Subnets | Public Subnet 1 (AZ1) + Public Subnet 2 (AZ2) |
| Security Group | HTTPAccess |
| Target Group | `webserver-app` → health check path: `/index.php` |

### Launch Template

| Setting | Value |
|---|---|
| Name | `web-app-launch-template` |
| AMI | WebServerAMI |
| Instance type | `t3.micro` |
| Security Group | HTTPAccess |

### Auto Scaling Group

| Setting | Value |
|---|---|
| Name | `Web App Auto Scaling Group` |
| Subnets | **Private** Subnet 1 + Private Subnet 2 |
| Load balancer | `webserver-app` target group |
| Desired / Min / Max | **2 / 2 / 4** |
| Scaling policy | Target tracking — Average CPU = **50%** |
| Instance tag | `Name: WebApp` |

> **Screenshot:** Auto Scaling Group → Activity tab — 2 instances launched

![asg-activity](screenshots/04-asg-activity.png)

---

## Task 3 — Verify Health and Trigger Scaling

Confirmed both `WebApp` instances reached **2/2 checks passed**, then verified **healthy** status in Target Group.

Opened ALB DNS in browser → clicked **Start Stress** → CPU on one instance spiked to 100%.

After ~5 minutes, Auto Scaling Group **Activity tab** showed a new instance being launched (CPU average exceeded 50% threshold).

> **Screenshot:** Target Group → Targets tab — both instances **healthy**

![targets-healthy](screenshots/05-targets-healthy.png)

> **Screenshot:** Auto Scaling Group → Activity tab — scale-out event triggered

![scale-out-event](screenshots/06-scale-out-event.png)

---

## CLI vs Console — This Lab's Approach

| Task | Method |
|---|---|
| Launch EC2 instance | **AWS CLI** |
| Create AMI | **AWS CLI** |
| Create ALB, Launch Template, ASG | AWS Management Console |

Using CLI for instance and AMI creation demonstrates that the same infrastructure operations available in the Console can be scripted and automated.

---

## Reflection

In this lab I learned how to:

- **Launch an EC2 instance entirely from the CLI** — using `run-instances` with flags for AMI, instance type, security group, subnet, user data, and tags in a single command; this is how infrastructure automation and CI/CD pipelines provision servers at scale
- **Use `aws ec2 wait`** to pause script execution until an instance reaches a specific state — a critical pattern for any automated deployment that needs to sequence steps safely
- **Create an AMI via CLI** with `create-image` — understanding that this operation temporarily stops the instance to ensure filesystem integrity before capturing the snapshot
- **Confirm the difference between Console-driven and CLI-driven workflows** — both produce identical infrastructure, but CLI commands are repeatable, version-controllable, and scriptable into pipelines

> Key takeaway: The AWS CLI is not just an alternative to the Console — it is the foundation of infrastructure as code. Every `aws ec2 run-instances` command can be saved in a script, committed to Git, and reproduced in any environment or account. This is the pattern behind CloudFormation, CDK, and Terraform.

---

*Kateryna | AWS Cloud Practitioner Exam Prep*
