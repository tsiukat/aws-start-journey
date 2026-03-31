
# Lab 05 — Working with Amazon EBS

> **Course:** AWS Cloud Practitioner (CLF-C02)  
> **Topic:** EBS Volumes, File Systems, Snapshots, Restore  
> **Date:** <!-- add date -->

---

## Lab Objective

Create and manage an Amazon EBS volume: attach it to an EC2 instance, configure a file system, take a snapshot backup, delete data, and restore it — demonstrating the full EBS data lifecycle.

---

## Tasks Completed

### ✅ Task 1 & 2 — Create and Attach an EBS Volume
- Created a **1 GiB gp2 EBS volume** named `My Volume` in the same Availability Zone as the Lab EC2 instance
- Attached it to the instance as device `/dev/sdb`

### ✅ Task 3 & 4 — Mount and Use the Volume
- Connected to the EC2 instance via **EC2 Instance Connect**
- Formatted the volume as **ext3** file system: `sudo mkfs -t ext3 /dev/sdb`
- Created mount point `/mnt/data-store` and mounted the volume
- Added the mount entry to `/etc/fstab` for persistence across reboots
- Wrote a test file to the volume: `echo some text has been written > /mnt/data-store/file.txt`

### ✅ Task 5 — Create a Snapshot
- Created a snapshot of `My Volume` named `My Snapshot`
- Deleted `file.txt` from the volume to simulate data loss

### ✅ Task 6 — Restore from Snapshot
- Created a new volume `Restored Volume` from `My Snapshot`
- Attached it to the EC2 instance as `/dev/sdc`
- Mounted it at `/mnt/data-store2`
- Confirmed `file.txt` was successfully restored

---

## Screenshots

### EBS Volume Created and Attached

<!-- Add screenshot: Volumes list showing My Volume in "In-use" state -->
![Volume Attached](screenshots/task2-volume-attached.png)

---

### File System Mounted — df -h Output

<!-- Add screenshot: terminal showing /dev/nvme1n1 mounted at /mnt/data-store -->
![Volume Mounted](screenshots/task4-volume-mounted.png)

---

### Snapshot Created

<!-- Add screenshot: Snapshots list showing My Snapshot with status "Completed" -->
![Snapshot Created](screenshots/task5-snapshot-completed.png)

---

### File Restored from Snapshot

<!-- Add screenshot: terminal output of `ls /mnt/data-store2/file.txt` showing the file exists -->
![File Restored](screenshots/task6-file-restored.png)

---

## Key Takeaways

| Concept | What I Learned |
|---------|----------------|
| **EBS Volume** | A network-attached block storage device — independent from the EC2 instance lifecycle |
| **Availability Zone Binding** | EBS volumes must be in the same AZ as the instance they attach to |
| **File System** | A raw EBS volume has no file system — you must format it (`mkfs`) before use |
| **`/etc/fstab`** | Ensures the volume is automatically remounted after an instance reboot |
| **EBS Snapshot** | A point-in-time backup stored in S3 — only changed blocks are saved, making it cost-efficient |
| **Restore from Snapshot** | Creates a fully independent new volume — data recovered even after deletion from the original |

---

## My Reflection

Before this lab, EBS felt abstract — "storage that attaches to EC2." After working through the full cycle, it became concrete. The most important insight was understanding that **EBS and EC2 are separate things**. The volume exists independently, survives instance stops, and can be detached and moved. That separation is what makes snapshots so powerful.

Formatting the volume with `mkfs` before use was a step I had not thought about before. A raw block device is just empty space — the file system is what makes it usable. Adding the mount to `/etc/fstab` to survive reboots was a small but practical detail that matters in any real deployment.

The snapshot restore was the most satisfying part: delete a file, spin up a new volume from a snapshot, mount it — and the file is back. That is backup and recovery made tangible, not just a diagram in a textbook.

> 💡 **Key Insight:** Snapshots are not just backups — they are a foundation for cloning environments, migrating data across AZs, and recovering from mistakes. Understanding the EBS lifecycle is essential for designing reliable, recoverable cloud infrastructure.

---

*Author: Kateryna | AWS Cloud Practitioner Exam Prep*
