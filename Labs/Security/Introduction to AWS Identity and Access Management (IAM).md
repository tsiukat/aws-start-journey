# Lab Introduction to AWS Identity and Access Management (IAM)

> **Course:** AWS Cloud Practitioner (CLF-C02)  
> **Topic:** IAM Users, User Groups, Policies, Password Policy, Permission Testing  


---

## Lab Objective

Explore IAM users, groups, and policies — assign users to groups with scoped permissions, then verify that each user can only access what their policy allows.

---

## Environment Overview

| User | Group | Policy | Permissions |
|------|-------|--------|-------------|
| user-1 | S3-Support | AmazonS3ReadOnlyAccess | Read-only S3 |
| user-2 | EC2-Support | AmazonEC2ReadOnlyAccess | Read-only EC2 |
| user-3 | EC2-Admin | EC2-Admin-Policy (inline) | View + Start/Stop EC2 |

---

## Tasks Completed

### ✅ Task 1 — Created a Custom IAM Password Policy
- Updated account-level password policy:
  - Minimum length: **10 characters**
  - Enabled: uppercase, lowercase, numbers, symbols, expiration (90 days), reuse prevention (5 passwords)
- Policy applies to all IAM users in the account

### ✅ Task 2 — Explored IAM Users, Groups, and Policies
- Reviewed 3 pre-created users — initially with no permissions and no group membership
- Inspected 3 user groups and their attached policies:
  - **EC2-Support** → `AmazonEC2ReadOnlyAccess` (managed policy)
  - **S3-Support** → `AmazonS3ReadOnlyAccess` (managed policy)
  - **EC2-Admin** → `EC2-Admin-Policy` (inline policy — view + start/stop EC2)

### ✅ Task 3 — Added Users to Groups
- user-1 → S3-Support
- user-2 → EC2-Support
- user-3 → EC2-Admin

### ✅ Task 4 — Tested Permissions for Each User

Signed in as each user via the IAM sign-in URL in a private browser window:

| User | S3 access | EC2 view | EC2 stop |
|------|-----------|----------|----------|
| user-1 | ✅ yes | ❌ not authorized | ❌ not authorized |
| user-2 | ❌ not authorized | ✅ yes | ❌ failed — read-only |
| user-3 | ❌ not authorized | ✅ yes | ✅ success |

---

## Screenshots

### Task 1 — Password Policy Configured
<img width="1366" height="728" alt="Password Policy Configured" src="https://github.com/user-attachments/assets/ef201d5b-b872-4e32-975e-1b7d0a01e357" />

---

### Task 2 — EC2-Admin Inline Policy
<img width="1366" height="728" alt="EC2-Admin Inline Policy" src="https://github.com/user-attachments/assets/c8a4e555-45f5-46ae-81a0-2afa5b4b828c" />
![Uploading Password Policy Configured.png…]()

---

### Task 3 — Users Added to Groups
<img width="1366" height="728" alt="Users Added to Groups" src="https://github.com/user-attachments/assets/1c4c0883-e269-434c-8cde-2a4fb88185ba" />

---<img width="1366" height="728" alt="User1 - s3" src="https://github.com/user-attachments/assets/9eb5c9e9-10f5-4adf-a79d-31f891260ad1" />


### Task 4 — user-1: S3 Access Allowed, EC2 Denied
<img width="1366" height="728" alt="User1 - s3" src="https://github.com/user-attachments/assets/5e4d88d8-b27a-4c8e-adaa-7c48c25b4b58" />
<img width="1366" height="728" alt="User1 - ec2 -denied" src="https://github.com/user-attachments/assets/a1c152da-4d33-4d10-8648-4163f5ee5efd" />

---

### Task 4 — user-2: EC2 View Allowed, Stop Denied
<img width="1366" height="728" alt="User2 - ec2 -read only - stop denied" src="https://github.com/user-attachments/assets/a26e7954-f86a-4890-804d-7b4d627bb5a0" />
<img width="1366" height="728" alt="User2 - s3" src="https://github.com/user-attachments/assets/26b983df-0d5a-4dc5-87d1-ce3400f07cdf" />


---

### Task 4 — user-3: EC2 Stop Successful
<img width="1366" height="728" alt="User3 - ec2" src="https://github.com/user-attachments/assets/427d1d3b-1d16-416f-8550-77cdaea6a44c" />


---

## Key Takeaways

| Concept | What I Learned |
|---------|----------------|
| **IAM User Group** | Attach permissions to a group, not individual users — users inherit permissions by membership |
| **Managed Policy** | Pre-built by AWS, reusable across multiple users and groups — updates apply instantly everywhere |
| **Inline Policy** | Attached to one specific group or user — used for one-off permission scenarios |
| **Least Privilege** | Each user has exactly the access their role requires — nothing more |
| **Policy Structure** | Every IAM policy has Effect (Allow/Deny), Action (what API calls), Resource (what it applies to) |
| **Password Policy** | Account-level setting — enforces complexity rules for all IAM users in the account |
| **IAM Sign-in URL** | Account-specific URL for IAM users — separate from the root account login |

---

## My Reflection

This lab made the principle of **least privilege** tangible rather than theoretical. Each user had exactly one scope of access, and the boundary between "allowed" and "denied" was crisp. Watching user-2 see EC2 instances but fail to stop them — with a clear error message — demonstrated that read-only permissions are a real technical constraint, not just a policy label.

The difference between **managed and inline policies** was also clarified. Managed policies are reusable and centrally maintained — change the policy once, and all attached users are updated. Inline policies are scoped to a single entity, useful for exceptions. Choosing between them is an architectural decision, not just a preference.

The password policy task reinforced something easy to overlook: IAM security starts before a user ever touches a service. A weak password policy is an open door regardless of how carefully the rest of the permissions are configured.

> 💡 **Key Insight:** IAM is not just access control — it is the security perimeter of your entire AWS account. Getting the group structure, policy types, and permission scopes right from the beginning is far easier than untangling overprivileged users later.

---

*Author: Kateryna | AWS Cloud Practitioner Exam Prep*
