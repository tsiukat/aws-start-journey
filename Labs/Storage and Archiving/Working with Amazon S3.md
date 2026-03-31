
# Lab 182 — Working with Amazon S3: Permissions, Sharing & Event Notifications

> **Course:** AWS Cloud Practitioner (CLF-C02)  
> **Topic:** S3 Bucket Policies, IAM User Permissions, SNS Notifications  
> **Date:** <!-- add date -->

---

## Lab Objective

Configure a secure S3 bucket for file sharing with an external user (`mediacouser`), verify scoped IAM permissions, and set up automated SNS email notifications triggered by bucket events.

---

## Tasks Completed

### ✅ Task 1 & 2 — Create and Populate the S3 Bucket
- Created an S3 bucket (`cafe-xxxnnn`) via AWS CLI: `aws s3 mb`
- Uploaded initial images using `aws s3 sync ~/initial-images/ s3://<bucket>/images`

### ✅ Task 3 — Review IAM Permissions and Test mediacouser Access
- Reviewed `mediaCoPolicy` attached to the `mediaco` IAM group — grants `GetObject`, `PutObject`, `DeleteObject` only within `cafe-*/images/*`
- Signed in as `mediacouser` and tested:
  - ✅ **View** — opened `Donuts.jpg` from the console
  - ✅ **Upload** — added a new image file
  - ✅ **Delete** — removed `Cup-of-Hot-Chocolate.jpg`
  - ❌ **Change bucket permissions** — blocked with `Insufficient permissions`

### ✅ Task 4 — Configure SNS Event Notifications
- Created SNS topic `s3NotificationTopic` and subscribed via email
- Granted S3 permission to publish to the topic via a custom SNS access policy
- Created `s3EventNotification.json` and attached it to the bucket with `aws s3api put-bucket-notification-configuration`
- Events tracked: `s3:ObjectCreated:*` and `s3:ObjectRemoved:*` under `images/` prefix

### ✅ Task 5 — Test Notifications via CLI as mediacouser
- **Put** `Caramel-Delight.jpg` → received SNS email: `ObjectCreated:Put`
- **Get** `Donuts.jpg` → no notification (reads are not tracked — by design)
- **Delete** `Strawberry-Tarts.jpg` → received SNS email: `ObjectRemoved:Delete`
- **Unauthorized ACL change** → `AccessDenied` error as expected

---

## Screenshots

### S3 Bucket Created and Images Synced
<img width="1920" height="1040" alt="task2-s3-sync" src="https://github.com/user-attachments/assets/ec05e5d4-b496-4b1f-8be4-06e00aee2189" />
<img width="1920" height="1040" alt="task2-s3-sync-1" src="https://github.com/user-attachments/assets/30d32af6-58a4-4082-92d9-679bd159e9e9" />
<img width="1366" height="728" alt="task3 upload succs" src="https://github.com/user-attachments/assets/3322cd05-dbcd-4fd7-9fcc-525c5df900ce" />
<img width="1920" height="1040" alt="task3 delete" src="https://github.com/user-attachments/assets/94a35360-2137-4d79-927a-7e6210566378" />

---

### mediacouser — Permission Denied on Bucket Settings
<img width="1920" height="1040" alt="task3 no permissions" src="https://github.com/user-attachments/assets/cfd4b2f4-402a-49cd-a46b-20741cff9a64" />
<img width="1920" height="1040" alt="task3 no permissions-1" src="https://github.com/user-attachments/assets/b433016d-ae43-44f9-9438-d1c30c57939f" />

---

### SNS Topic Created and Subscription Confirmed
<img width="1920" height="1040" alt="task4 subscription confirmed" src="https://github.com/user-attachments/assets/ee798b78-fb2c-4ef2-b82c-50f7237975a0" />

---

### SNS Email — ObjectCreated Notification
<img width="1920" height="1040" alt="task5-email-object-created" src="https://github.com/user-attachments/assets/03c44094-f53a-4646-bc0b-7c773d558cf3" />

---

### SNS Email — ObjectRemoved Notification
<img width="1920" height="1040" alt="task5-email-object-removed" src="https://github.com/user-attachments/assets/15dac785-dfbf-4cb1-98fd-b37496b4f633" />


---

### Unauthorized ACL Change — AccessDenied
<img width="1920" height="1040" alt="task5-access-denied" src="https://github.com/user-attachments/assets/b37b55d3-3be6-4443-a92c-83d540375d88" />

---

## Key Takeaways

| Concept | What I Learned |
|---------|----------------|
| **Scoped IAM Policy** | Permissions can be limited to a specific S3 prefix (`cafe-*/images/*`) — fine-grained access without exposing the whole bucket |
| **Principle of Least Privilege** | `mediacouser` can read, write, and delete objects but cannot change bucket-level permissions — exactly the right scope for an external collaborator |
| **SNS + S3 Integration** | S3 can publish events to SNS automatically — no polling, no custom code needed |
| **Event Filtering** | Notifications can be scoped to specific prefixes and event types — only `images/` changes trigger alerts, not every bucket action |
| **Read vs Write Events** | `GetObject` does not trigger notifications by default — S3 only tracks mutations (create/delete) unless explicitly configured otherwise |
| **AccessDenied as a Feature** | A rejected unauthorized action is the system working correctly — denial is not an error, it is a security outcome |

---

## My Reflection

This lab shifted my thinking about S3 from a storage tool to an **integrated part of a workflow**. Connecting S3 to SNS with a JSON configuration file made event-driven architecture concrete — something changes in the bucket, an email arrives automatically, no manual monitoring needed. In a real café or FinTech context, that is how operations teams stay informed without having to check dashboards constantly.

The IAM permissions review was equally valuable. Seeing exactly which actions `mediacouser` can and cannot perform — and then verifying each one live — made the policy syntax feel purposeful rather than bureaucratic. The unauthorized ACL attempt returning `AccessDenied` was satisfying: not a failure, but proof that the security boundary held.

The detail that stood out most: **reads do not trigger notifications by default**. That is an important design decision to understand — if you need read auditing, you have to configure it explicitly. Assuming all actions are tracked would be a real mistake in a compliance-sensitive environment.

> 💡 **Key Insight:** S3 is not just a bucket — it is an event source, a permission boundary, and a collaboration interface. How you configure it determines how securely and how observably your data moves.

---

*Author: Kateryna | AWS Cloud Practitioner Exam Prep*
