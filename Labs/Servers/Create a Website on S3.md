
# Lab Activity - Create a Website on S3

> **Course:** AWS Cloud Practitioner (CLF-C02)  
> **Topic:** Amazon S3, IAM, AWS CLI, Static Website Hosting  
> **Date:** <SUBMITTED: Mar 17, 2026 7:46pm>

---

## Table of Contents

1. [Lab Objective](#lab-objective)
2. [Tasks Completed](#tasks-completed)
3. [Screenshots](#screenshots)
4. [Key Takeaways](#key-takeaways)
5. [My Reflection](#my-reflection)

---

## Lab Objective

Use the AWS CLI from an EC2 instance to create an S3 bucket, configure IAM permissions, deploy a static website, and automate future updates with a reusable bash script.

---

## Tasks Completed

### ✅ Task 1 — Connect to EC2 via AWS Systems Manager (Session Manager)
- Connected to the instance without SSH keys using **SSM Session Manager**
- Switched to `ec2-user` for the rest of the lab

### ✅ Task 2 — Configure the AWS CLI
- Ran `aws configure` with lab credentials (Access Key, Secret Key)
- Set default region: **us-west-2**, output format: **json**

### ✅ Task 3 — Create an S3 Bucket via AWS CLI
- Used `aws s3api create-bucket` with `--region` and `LocationConstraint` flags
- Bucket name must be globally unique across all AWS accounts

### ✅ Task 4 — Create an IAM User with Full S3 Access
- Created IAM user `awsS3user` with `aws iam create-user`
- Set a login profile (console password)
- Found and attached the AWS managed policy `AmazonS3FullAccess` using `aws iam list-policies`

### ✅ Task 5 — Adjust S3 Bucket Permissions
- Disabled **Block all public access** on the bucket
- Enabled **ACLs** under Object Ownership to allow public-read on uploaded objects

### ✅ Task 6 — Extract Lab Files on the EC2 Instance
- Extracted `static-website-v2.tar.gz` to get `index.html`, `css/`, and `images/`

### ✅ Task 7 — Upload Files to S3 and Enable Static Website Hosting
- Configured the bucket as a website: `aws s3 website s3://<bucket>/ --index-document index.html`
- Uploaded all files with `aws s3 cp --recursive --acl public-read`
- Verified the site is publicly accessible via the **Bucket website endpoint URL**

### ✅ Task 8 — Create a Bash Script for Repeatable Deployments
- Created `update-website.sh` with the `aws s3 cp` command
- Made it executable with `chmod +x`
- Edited `index.html` (changed background colors) and redeployed via the script
- Explored `aws s3 sync` as a more efficient alternative — uploads only changed files

---

## Screenshots

### Task 2 — AWS CLI Configured

![Task 2](https://github.com/user-attachments/assets/92f91440-f582-4cc9-9e04-276ea6f288f1)

---

### Task 3 — S3 Bucket Created

<img width="1361" height="157" alt="Task 3" src="https://github.com/user-attachments/assets/68de50ab-6b35-4bfa-877b-e4908f58ce0e" />

---

### Task 4 — IAM User and Policy Attached

<img width="1360" height="689" alt="Task 4 awsS3user" src="https://github.com/user-attachments/assets/c5cf1b75-d59c-46d4-88bd-23e7bc11d53f" />

<img width="1366" height="728" alt="Task 4 awsS3user -permision" src="https://github.com/user-attachments/assets/d23f02ef-85ae-4849-b555-d85ad8fc2b28" />


---

### Task 5 — Bucket Permissions Updated

<img width="1366" height="728" alt="Task 7 Upload files to Amazon S3 by using the AWS CLI" src="https://github.com/user-attachments/assets/2ac92e59-497c-4432-9854-66efb4fae40c" />

---

### Task 7 — Website Live in Browser & Updated Website After Script Run

<img width="1366" height="728" alt="Task 7 Upload files to Amazon S3" src="https://github.com/user-attachments/assets/3eb123f7-f2dc-45a3-a126-43382e00b4a2" />

<img width="656" height="611" alt="website updated" src="https://github.com/user-attachments/assets/de821492-fb79-4c31-9340-a0ff74364afb" />

---

## Key Takeaways

| Concept | What I Learned |
|---------|----------------|
| **S3 Static Website Hosting** | Any S3 bucket can serve as a web host — just enable the feature and set an index document |
| **Bucket Naming** | S3 bucket names must be globally unique across all AWS accounts and regions |
| **AWS CLI vs Console** | CLI enables scripting and automation; console is better for visual exploration |
| **IAM + S3** | Access to S3 is not automatic — users need explicit policies attached to perform actions |
| **Block Public Access** | S3 buckets are private by default; public hosting requires intentionally disabling this setting |
| **ACLs** | Object-level ACLs (`--acl public-read`) control who can read individual files |
| **`s3 cp` vs `s3 sync`** | `cp` uploads all files every time; `sync` compares and uploads only what changed — more efficient |
| **Bash scripting** | A simple shell script can automate repetitive deployment tasks and make updates one-command repeatable |

---

## My Reflection

This lab shifted my understanding of S3 from "a storage bucket" to "a deployment target." Before this, I knew S3 stored files. Now I know it can serve a public website with zero server infrastructure — no EC2, no web server process, no OS to maintain. That is a meaningful architectural simplification.

The IAM portion reinforced something important: in AWS, **no access is the default**. Even after creating the `awsS3user`, they couldn't see the bucket until I found and attached the right managed policy. In a FinTech context, this maps directly to the principle of least privilege — users and services should only have exactly the permissions they need, nothing more.

The most practically valuable part was **Task 8**. Writing the deployment script made me think like a developer, not just a student following steps. The difference between `aws s3 cp` and `aws s3 sync` is also a real-world decision: `sync` is smarter and cheaper because it avoids redundant uploads. In production, where buckets can hold thousands of files, that distinction matters for both speed and cost.

Using **Session Manager** instead of SSH to connect to EC2 was also a quiet but important detail — no key pair, no open port 22, full audit trail. That is the modern, secure way to access instances.

> 💡 **Key Insight:** S3 is not just storage — it is infrastructure. Combining it with IAM, CLI automation, and public access controls turns a simple bucket into a scalable, serverless hosting solution that can be updated with a single script.

---

*Author: Kateryna | AWS Cloud Practitioner Exam Prep*
