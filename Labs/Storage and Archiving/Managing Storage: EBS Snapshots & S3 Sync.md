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

<!-- Add screenshot: terminal output of aws ec2 create-snapshot with SnapshotId returned -->
![Snapshot Created](screenshots/task2-snapshot-created.png)

---

### Cron Job Running — Multiple Snapshots Generated

<!-- Add screenshot: aws ec2 describe-snapshots output showing multiple snapshots -->
![Multiple Snapshots](screenshots/task2-cron-snapshots.png)

---

### Snapshotter Script — Cleanup Result

<!-- Add screenshot: terminal output of snapshotter_v2.py showing deleted snapshots -->
![Snapshot Cleanup](screenshots/task2-snapshotter-cleanup.png)

<!-- Add screenshot: describe-snapshots output showing only 2 snapshots remain -->
![Two Snapshots Remain](screenshots/task2-two-snapshots.png)

---

### S3 Sync — Files Uploaded

<!-- Add screenshot: terminal output of aws s3 sync showing 3 files uploaded -->
![S3 Sync Upload](screenshots/task3-s3-sync-upload.png)

---

### File Deleted and Recovered via Versioning

<!-- Add screenshot: aws s3 ls showing file1.txt missing after --delete sync -->
![File Deleted](screenshots/task3-file-deleted.png)

<!-- Add screenshot: terminal showing file1.txt restored and re-synced to S3 -->
![File Restored](screenshots/task3-file-restored.png)

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
