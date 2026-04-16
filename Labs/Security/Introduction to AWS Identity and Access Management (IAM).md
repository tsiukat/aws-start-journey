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

<!-- Add screenshot: IAM Account settings showing updated password policy -->
![Password Policy](screenshots/task1-password-policy.png)

---

### Task 2 — EC2-Admin Inline Policy

<!-- Add screenshot: EC2-Admin-Policy expanded showing Describe + Start/Stop EC2 actions -->
![Inline Policy](screenshots/task2-ec2-admin-policy.png)

---

### Task 3 — Users Added to Groups

<!-- Add screenshot: IAM User groups list showing 1 user in each group -->
![Users in Groups](screenshots/task3-users-in-groups.png)

---

### Task 4 — user-1: S3 Access Allowed, EC2 Denied

<!-- Add screenshot: user-1 viewing S3 buckets successfully -->
![user-1 S3](screenshots/task4-user1-s3-access.png)

<!-- Add screenshot: user-1 seeing "Not authorized" in EC2 Instances -->
![user-1 EC2 Denied](screenshots/task4-user1-ec2-denied.png)

---

### Task 4 — user-2: EC2 View Allowed, Stop Denied

<!-- Add screenshot: user-2 seeing "Failed to stop the instance" error -->
![user-2 Stop Denied](screenshots/task4-user2-stop-denied.png)

---

### Task 4 — user-3: EC2 Stop Successful

<!-- Add screenshot: EC2 instance entering "Stopping" state under user-3 -->
![user-3 Stop Success](screenshots/task4-user3-stop-success.png)

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
# Lab: Introduction to AWS Identity and Access Management (IAM)

> **Course:** AWS Cloud Practitioner (CLF-C02)  
> **Topic:** Security
> 

The primary goal was to implement the Principle of Least Privilege (PoLP) by configuring security policies, users, and groups within an AWS environment.

## 🎯 Objectives
- Configure and apply a custom IAM Password Policy.
- Organize IAM users into functional groups (S3 Support, EC2 Support, EC2 Admin).
- Inspect and analyze Managed vs. Inline IAM policies.
- Validate permissions by testing cross-service access for different user roles.

## 🛠 Key Implementation Steps1. 

### 1. Hardening Account Security (Password Policy)
To improve the security posture of the AWS account, I implemented a strict password policy:

Minimum length: 10 characters.

Complexity: Required uppercase, lowercase, numbers, and non-alphanumeric characters.

Prevention: Users cannot reuse their last 5 passwords.

[Screenshot 1: Custom Password Policy settings in the IAM Dashboard]

### 2. User and Group Management
I mapped users to specific business roles to ensure operational efficiency and security:

User,Assigned Group,Permissions (IAM Policies)

user-1,S3-Support,AmazonS3ReadOnlyAccess (View buckets/content)

user-2,EC2-Support,AmazonEC2ReadOnlyAccess (View EC2 status)

user-3,EC2-Admin,"EC2-Admin-Policy (View, Start, and Stop EC2 instances)"

[Screenshot 2: List of IAM Users assigned to their respective Groups]

### 3. Testing Permission Boundaries (Validation)
I logged in as each user via the unique IAM Sign-in URL to verify that permissions were correctly applied:

User-1 Test: Successfully listed S3 buckets but received an "API Error/Not Authorized" when trying to view EC2 instances.

User-2 Test: Could view running EC2 instances but was blocked when attempting to "Stop Instance" (Access Denied).

User-3 Test: Successfully performed administrative actions (Stopping an EC2 instance).

[Screenshot 3: "Not Authorized" error message when user-2 attempted to stop an instance]
[Screenshot 4: Successful "Stopping" state for the EC2 instance under user-3]

## 💡 Key Takeaways
## Scalability with Groups: 
Managing permissions at the group level is significantly more efficient than assigning policies to individual users.
## Policy Structure: 
Gained a deep understanding of the Effect, Action, and Resource elements within JSON policy documents.
## IAM Dashboard Tools: 
Learned how to locate and use the account-specific Sign-in URL for secure team access.

## 🧠 Reflection
Through this lab, I gained hands-on experience in implementing the AWS security model. The most insightful part was witnessing "real-time" enforcement of the Least Privilege principle—seeing exactly how a user can have "Read" access but be strictly blocked from "Write" or "Modify" actions. This exercise clarified how large-scale organizations protect their infrastructure from both accidental errors and unauthorized changes. I now feel confident in my ability to manage identities, audit permissions, and delegate authority within a cloud environment.
