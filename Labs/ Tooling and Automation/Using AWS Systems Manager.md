# Lab: Using AWS Systems Manager

**Course:** AWS Cloud Practitioner Exam Prep  
**Topic:** AWS Systems Manager — Fleet Manager, Run Command, Parameter Store, Session Manager  

---

## Overview

AWS Systems Manager enables centralized operational management of EC2 instances and servers **without SSH access**. This lab covers four core SSM capabilities used to inventory, configure, and access instances programmatically.

| Capability | Purpose |
|---|---|
| **Fleet Manager / Inventory** | Collect software/OS metadata from instances |
| **Run Command** | Execute scripts/commands remotely without SSH |
| **Parameter Store** | Store and retrieve configuration values securely |
| **Session Manager** | Browser-based shell access — no SSH keys or open ports |

---

## Task 1 — Generate Inventory with Fleet Manager

In **Systems Manager → Fleet Manager → Account management → Set up inventory**:

| Setting | Value |
|---|---|
| Name | `Inventory-Association` |
| Targets | Manually selecting instances → Managed Instance |

After setup, navigated to **Node ID → Inventory tab** — listed all installed applications and OS metadata on the instance without logging into it.

> **Screenshot:** Fleet Manager → Inventory tab — installed applications list

![inventory-apps](screenshots/01-inventory-apps.png)

---

## Task 2 — Install Application via Run Command

In **Systems Manager → Run Command → Run command**:

Selected the custom document:
- **Owner:** Owned by me
- **Document:** `Install Dashboard App`
- **Target:** Managed Instance (manually selected)
- **S3 output:** Disabled

**What the document installs:**
- Apache web server
- PHP
- AWS SDK
- Widget Manufacturing Dashboard web app

After ~2 minutes → **Overall status: Success**

Opened `http://ServerIP` in browser → Widget Manufacturing Dashboard loaded. ✅

All done **without SSH** — Run Command pushed and executed the install script remotely via the SSM agent.

> **Screenshot:** Systems Manager → Run Command → Overall status = **Success**

![run-command-success](screenshots/02-run-command-success.png)

> **Screenshot:** Browser — Widget Manufacturing Dashboard installed and running

![dashboard-installed](screenshots/03-dashboard-installed.png)

---

## Task 3 — Manage App Settings via Parameter Store

In **Systems Manager → Parameter Store → Create parameter**:

| Setting | Value |
|---|---|
| Name | `/dashboard/show-beta-features` |
| Description | Display beta features |
| Type | String (default) |
| Value | `True` |

Refreshed the browser tab with the dashboard → a **third chart appeared** (previously hidden beta feature).

The application checks Parameter Store at runtime — no code deployment or restart needed. This is the **feature flag / dark launch** pattern.

Optional test: deleted the parameter → third chart disappeared again.

> **Screenshot:** Browser — dashboard with 3 charts after Parameter Store value set to `True`

![beta-feature-enabled](screenshots/04-beta-feature-enabled.png)

---

## Task 4 — Access Instance via Session Manager

In **Systems Manager → Session Manager → Start session → Managed Instance → Start session**

Browser-based terminal opened. No SSH key, no open inbound port required.

**Commands run in the session:**

```bash
# List installed web app files
ls /var/www/html

# Get instance region from metadata
AZ=`curl -s http://169.254.169.254/latest/meta-data/placement/availability-zone`
export AWS_DEFAULT_REGION=${AZ::-1}

# Query EC2 instance details via CLI
aws ec2 describe-instances
```

> **Screenshot:** Session Manager — browser terminal with `ls /var/www/html` output

![session-manager-shell](screenshots/05-session-manager-shell.png)

---

## SSH vs Session Manager

| Feature | SSH | Session Manager |
|---|---|---|
| Inbound port 22 required | Yes | **No** |
| Key pair management | Yes | **No** |
| Access control | OS-level users | **IAM policies** |
| Audit trail | Manual / none | **CloudTrail logs every session** |
| Works without public IP | No | **Yes** |

Session Manager is the recommended approach for production instance access in AWS.

---

## Systems Manager Capabilities — Summary

```
EC2 Instance (with SSM Agent installed)
    │
    ├── Fleet Manager → inventory software/OS without SSH
    ├── Run Command  → execute scripts remotely without SSH
    ├── Parameter Store → read config values at runtime
    └── Session Manager → interactive shell without SSH/port 22
```

---

## Reflection

In this lab I learned how to:

- **Use Fleet Manager to audit instances at scale** — viewing installed applications from the Systems Manager console without connecting to each instance individually is how large fleets are managed in practice
- **Deploy applications with Run Command** — pushing an install script via SSM avoids the need for bastion hosts, SSH key distribution, or manual login; this is how configuration management tools like Ansible integrate with AWS
- **Implement feature flags with Parameter Store** — storing `/dashboard/show-beta-features = True` as a runtime parameter and having the application check it shows how teams enable/disable features without redeploying code
- **Use Session Manager as a secure SSH replacement** — no open port 22, no key pair rotation, full CloudTrail audit trail; this is the current AWS best practice for interactive instance access

> Key takeaway: AWS Systems Manager eliminates the operational overhead of SSH-based instance management. No bastion hosts, no key pairs, no open ports — just IAM-controlled, CloudTrail-audited access and automation at scale. This is the modern pattern for managing EC2 fleets in production.

---

*Kateryna | AWS Cloud Practitioner Exam Prep*
