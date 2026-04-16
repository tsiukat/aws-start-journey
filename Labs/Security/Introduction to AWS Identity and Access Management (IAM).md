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
