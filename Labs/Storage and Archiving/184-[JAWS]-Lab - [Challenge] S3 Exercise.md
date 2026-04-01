
# Challenge Lab — Amazon S3: Bucket, Permissions & CLI Access

> **Course:** AWS Cloud Practitioner (CLF-C02)  
> **Topic:** S3 Bucket Creation, Object Permissions, Public Access, AWS CLI  
> **Date:** 01.04.26

---

## Lab Objective

Independent challenge — create an S3 bucket, upload an object, configure public access permissions, verify it in a browser, and list bucket contents via the AWS CLI. No step-by-step instructions provided.

---

## What I Did

### ✅ Connected to CLI Host and Configured AWS CLI
- Connected via EC2 Instance Connect
- Ran `aws configure` with lab credentials (AccessKey, SecretKey, region `us-west-2`)

### ✅ Created an S3 Bucket
- Created a uniquely named S3 bucket via AWS Console or CLI

### ✅ Uploaded an Object
- Uploaded an image or file into the bucket

### ✅ Attempted Public Access — Blocked by Default
- Tried to open the object URL in a browser → access denied
- S3 blocks all public access by default

### ✅ Made the Object Publicly Accessible
- Disabled **Block Public Access** on the bucket
- Set object-level public read permission (ACL or bucket policy)
- Verified the object loads successfully in the browser

### ✅ Listed Bucket Contents via AWS CLI
- Ran `aws s3 ls s3://<bucket-name>/` to confirm objects are present

---

## Screenshots

### Bucket Created

<!-- Add screenshot: S3 console showing the new bucket in the list -->
![Bucket Created](screenshots/bucket-created.png)

---

### Object Uploaded

<!-- Add screenshot: bucket contents showing the uploaded file -->
![Object Uploaded](screenshots/object-uploaded.png)

---

### Access Denied — Before Permissions Fix

<!-- Add screenshot: browser showing Access Denied on the object URL -->
![Access Denied](screenshots/access-denied.png)

---

### Object Accessible in Browser — After Fix

<!-- Add screenshot: browser successfully loading the uploaded object -->
![Object Public](screenshots/object-public.png)

---

### AWS CLI — Bucket Contents Listed

<!-- Add screenshot: terminal output of `aws s3 ls s3://<bucket>/` -->
![CLI List](screenshots/cli-bucket-list.png)

---

## Key Takeaways

| Concept | What I Learned |
|---------|----------------|
| **S3 Default Security** | All objects are private by default — public access requires explicit configuration |
| **Object vs Bucket Permissions** | Making a bucket public does not automatically make objects public — permissions work at both levels |
| **Browser Access** | An S3 object URL only works publicly after both Block Public Access is disabled and the object has a public-read permission |
| **`aws s3 ls`** | Simple CLI command to verify bucket contents without opening the console |

---

## My Reflection

This was the first **challenge lab** — no guided steps, just objectives. That made a real difference. Knowing what to do and actually doing it independently are two different things, and this lab sat at that boundary.

The most valuable moment was hitting **Access Denied** in the browser after uploading the file. In previous labs, permissions were set as part of the instructions. Here I had to diagnose it myself — remember that S3 blocks public access by default, find the right setting, apply it, and verify. That sequence — observe, diagnose, fix, confirm — is what real cloud work looks like.

The CLI listing at the end was a small but satisfying close: the same result as the console, one command, no clicking.

> 💡 **Key Insight:** A challenge lab without instructions tests whether you understood the concept or just followed the steps. Getting the object to load in the browser by working through the permissions independently made this one of the most memorable labs so far.

---

*Author: Kateryna | AWS Cloud Practitioner Exam Prep*
