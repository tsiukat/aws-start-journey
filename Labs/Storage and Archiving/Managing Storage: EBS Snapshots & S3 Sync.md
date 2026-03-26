# Lab 06 — Managing Storage: EBS Snapshots & S3 Sync

> **Course:** AWS Cloud Practitioner (CLF-C02)  
> **Topic:** EBS Snapshots, Cron Automation, S3 Sync, S3 Versioning  
> **Date:** <!-- add date -->

---

## Lab Objective

Automate EBS snapshot management using the AWS CLI and cron, then sync files from an EBS volume to S3 — and recover a deleted file using S3 versioning.

---

## Tasks Completed

### ✅ Task 1 — Setup: S3 Bucket and IAM Role
- Created an S3 bucket for file sync
- Attached **S3BucketAccess** IAM role to the `Processor` instance via instance profile

### ✅ Task 2 — EBS Snapshot Management (via Command Host)

**Initial snapshot:**
- Retrieved Volume ID and Instance ID of the `Processor` instance using `aws ec2 describe-instances`
- Stopped the instance → created a snapshot → restarted the instance

**Automated snapshots with cron:**
- Scheduled a cron job to create a new snapshot every minute
- Verified multiple snapshots were created with `aws ec2 describe-snapshots`

**Snapshot cleanup with Python:**
- Stopped the cron job with `crontab -r`
- Ran `snapshotter_v2.py` — a script that keeps only the **2 most recent snapshots** and deletes the rest
- Confirmed only 2 snapshot IDs remained

### ✅ Task 3 — S3 Sync and Versioning (via Processor)
- Downloaded and unzipped sample files (`file1.txt`, `file2.txt`, `file3.txt`)
- Enabled **S3 bucket versioning** with `aws s3api put-bucket-versioning`
- Synced local folder to S3 with `aws s3 sync`
- Deleted `file1.txt` locally → re-synced with `--delete` flag → file removed from S3
- Recovered deleted file using `aws s3api list-object-versions` + `aws s3api get-object --version-id`
- Re-synced restored file back to S3

---

## Screenshots

### Snapshot Created — Initial

<img width="1920" height="1040" alt="task2-snapshot-created" src="https://github.com/user-attachments/assets/cb96626d-da5f-422b-bade-a259fea9a40c" />

---

### Cron Job Running — Multiple Snapshots Generated

<img width="1920" height="1040" alt="task2-cron-snapshots" src="https://github.com/user-attachments/assets/12801fec-2e10-4788-abfb-6c0958ecc174" />
<img width="1920" height="1040" alt="task2-cron-snapshots-1" src="https://github.com/user-attachments/assets/11793992-83b7-4bee-a538-f3e8dac7382b" />


---

### Snapshotter Script — Cleanup Result

<img width="1920" height="1040" alt="task2-snapshotter-cleanup" src="https://github.com/user-attachments/assets/80ffbd4f-4914-4d70-8631-fc81bbb30bf6" />
<img width="1920" height="1040" alt="task2-two-snapshots" src="https://github.com/user-attachments/assets/7362fea8-b560-4c86-995f-01d14c0679d5" />

---

### File Deleted and Recovered via Versioning

<img width="1920" height="1040" alt="task3-file-deleted" src="https://github.com/user-attachments/assets/bf94e16b-7e36-426a-81b2-8e8e44b4e98c" />
<img width="1920" height="1040" alt="task3-file-restored" src="https://github.com/user-attachments/assets/c6d42254-9064-4968-863c-a38e2859540c" />


---

## Key Takeaways

| Concept | What I Learned |
|---------|----------------|
| **EBS Snapshot via CLI** | Snapshots can be fully automated — no console needed, scriptable end to end |
| **Cron + AWS CLI** | Linux cron scheduler can trigger any AWS CLI command on a time-based schedule |
| **Snapshot Retention Policy** | Keeping unlimited snapshots is costly — automated cleanup scripts enforce a retention limit |
| **`aws s3 sync`** | Syncs only changed files — more efficient than copying everything every time |
| **`--delete` flag** | Without it, `sync` never removes files from S3 even if deleted locally — important to understand the default behavior |
| **S3 Versioning** | Deleted objects are not gone — they get a delete marker, and previous versions remain recoverable |
| **IAM Instance Profile** | Gives EC2 instances permissions to interact with other AWS services without storing credentials |

---

## My Reflection

This lab connected two things I had studied separately — EBS and S3 — into one practical storage management workflow. The cron + CLI combination made automation feel real: instead of clicking through the console to create snapshots, a single line schedules it indefinitely. Running `snapshotter_v2.py` and watching it trim everything down to exactly two snapshots made the concept of a **retention policy** concrete rather than theoretical.

The S3 versioning section was the most surprising part. Deleting a file locally, syncing with `--delete`, and watching it disappear from S3 — then recovering it with a version ID — demonstrated that S3 versioning is not just a checkbox, it is a genuine safety net. The fact that there is no direct "restore" command forces you to understand what versioning actually does: it keeps all versions with delete markers, and recovery means explicitly fetching an older version.

> 💡 **Key Insight:** Storage management is not just about saving data — it is about controlling what stays, what goes, and what can be recovered. Snapshots, sync, and versioning each solve a different part of that problem.

---

*Author: Kateryna | AWS Cloud Practitioner Exam Prep*
