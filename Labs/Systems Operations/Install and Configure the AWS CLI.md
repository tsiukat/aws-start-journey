# Lab: Install and Configure the AWS CLI

**Course:** AWS Cloud Practitioner Exam Prep  
**Topic:** AWS CLI — Installation, IAM Configuration, Policy Retrieval  

---

## Overview

Installed the AWS CLI on a **Red Hat Linux EC2 instance** (does not have CLI pre-installed, unlike Amazon Linux), configured it with IAM credentials, and used it to interact with IAM programmatically.

**Architecture:**

```
[Local machine] --SSH--> [Red Hat EC2 instance]
                              │
                         AWS CLI configured
                              │
                         IAM (awsstudent user)
```

---


## Task 2 — Install AWS CLI v2

```bash
# Download installer
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

# Unzip
unzip -u awscliv2.zip

# Install
sudo ./aws/install

# Verify
aws --version
```

Expected output: `aws-cli/2.x.x Python/3.x.x Linux/...`

> **Screenshot:** Terminal — `aws --version` showing installed version

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/ee742cbd-cdb1-48c1-a1cd-fa0cf7d53dd4" />


---

## Task 3 — Review IAM User in Console

In **IAM Console → Users → awsstudent**:
- **Permissions tab** → `lab_policy` → JSON format shows allowed AWS services
- **Security credentials tab** → Access Key ID for CLI authentication

> **Screenshot:** IAM Console → awsstudent → lab_policy JSON expanded

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/78321996-c33b-4296-bf47-c95c0a71d2ac" />

---

## Task 4 — Configure AWS CLI

```bash
aws configure
```

| Prompt | Value |
|---|---|
| AWS Access Key ID | from Details → AccessKey |
| AWS Secret Access Key | from Details → SecretKey |
| Default region | `us-west-2` |
| Default output format | `json` |

> **Screenshot:** Terminal — `aws configure` prompts filled in

![aws-configure](screenshots/04-aws-configure.png)

---

## Task 5 — Query IAM via CLI

**Verify connection — list IAM users:**

```bash
aws iam list-users
```

Returns a JSON response with all IAM users in the account.

> **Screenshot:** Terminal — `aws iam list-users` JSON output

![list-users](screenshots/05-list-users.png)

---

## Challenge — Download lab_policy as JSON via CLI only

**Step 1 — Find the policy ARN and version:**

```bash
aws iam list-policies --scope Local
```
> **Screenshot:** Terminal — Retrieving and exporting the IAM "lab_policy" JSON document via AWS CLI
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/a2f3b6c7-4678-4705-866a-f6d169f4b172" />

**Step 2 — Retrieve the policy document and save to file:**

```bash
aws iam get-policy-version --policy-arn arn:aws:iam::445756104209:policy/lab_policy --version-id v1 > lab_policy.json
```

> **Screenshot:** Terminal — `lab_policy.json` saved, contents shown

<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/c0b25560-83a3-4077-b44a-09a286c27605" />


---

## CLI vs Console — Key Difference

| Task | Console | CLI |
|---|---|---|
| Authentication | Username + password | Access Key ID + Secret Access Key |
| Interaction | Graphical (point and click) | Commands + flags |
| Automation | Manual only | Scriptable, repeatable |
| Speed at scale | Slow | Fast — one command vs many clicks |

---

## Reflection

In this lab I learned how to:

- **Install AWS CLI v2 manually** on a Red Hat instance — understanding that not all AMIs come pre-configured, and knowing the curl → unzip → install pattern is foundational for any cloud automation workflow
- **Distinguish CLI authentication from Console authentication** — the CLI uses an Access Key ID + Secret Access Key pair, not a username/password; this is a key security concept for IAM and the CLF-C02 exam
- **Use `aws configure`** to connect the CLI to a specific AWS account and region, and understand that these credentials are stored locally and scoped to the configured user's permissions
- **Navigate AWS CLI documentation** to find and chain commands — `list-policies --scope Local` to find the ARN, then `get-policy-version` to retrieve the actual document — instead of relying on the Console

> Key takeaway: The AWS CLI is the bridge between manual Console work and full automation. Every action possible in the Console can be scripted via CLI — which is how infrastructure at scale (CloudFormation, CI/CD pipelines, Lambda triggers) is built and managed in real production environments.

---

*Kateryna | AWS Cloud Practitioner Exam Prep*
