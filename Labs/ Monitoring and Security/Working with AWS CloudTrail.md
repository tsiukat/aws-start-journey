
# Lab: Working with AWS CloudTrail

**Course:** AWS Cloud Practitioner Exam Prep  
**Topic:** CloudTrail Audit Logs - Security Incident Investigation + Athena Log Analysis  


---

## Scenario

The café website was hacked. My role (as Sofía): identify who made unauthorized changes to the AWS security group and EC2 instance, remove the attacker's access, and harden the environment.

**Investigation flow:**

```
Website hacked → CloudTrail logs → grep + AWS CLI → Athena SQL queries
                                                            ↓
                                                  Identified: chaos user
                                                            ↓
                                       Removed OS user · Deleted IAM user
                                       Fixed SSH config · Restored website
```

---

## Task 1 - Observe Website + Modify Security Group

Opened café website → normal state confirmed.

Added SSH inbound rule (port 22, My IP only) to the Web Server security group as a baseline legitimate change - this establishes my own fingerprint in the CloudTrail logs for comparison.

> **Screenshot:** EC2 → Security Group → Inbound rules - SSH port 22, source = My IP

![sg-before-hack](screenshots/01-sg-before-hack.png)

---

## Task 2 - Create CloudTrail Trail + Observe the Hack

In **CloudTrail → Create trail**:

| Setting | Value |
|---|---|
| Trail name | `monitor` |
| S3 bucket | `monitoring####` (new) |
| KMS alias | `initials-KMS` |

Refreshed the café website after ~1 minute → site was **hacked** (inappropriate image displayed).

Returned to the Security Group → found a **new inbound rule**: SSH from `0.0.0.0/0` (anyone on the internet). Someone added this while I was setting up the trail.

> **Screenshot:** EC2 → Security Group → Inbound rules showing unauthorized SSH rule from 0.0.0.0/0

![sg-hacked](screenshots/02-sg-hacked.png)

---

## Task 3 - Analyze CloudTrail Logs via grep + AWS CLI

SSH into the Café Web Server, downloaded logs from S3:

```bash
mkdir ctraillogs && cd ctraillogs
aws s3 cp s3://monitoring####/ . --recursive
gunzip *.gz
```

### grep analysis

```bash
# Find events by source IP (web server's own IP)
for i in $(ls); do echo $i && cat $i | python -m json.tool | grep sourceIPAddress; done

# Find all event names across log files
for i in $(ls); do echo $i && cat $i | python -m json.tool | grep eventName; done
```

### AWS CLI CloudTrail lookup

```bash
# Find all events on the web server's security group
region=$(curl http://169.254.169.254/latest/dynamic/instance-identity/document | grep region | cut -d '"' -f4)
sgId=$(aws ec2 describe-instances \
  --filters "Name=tag:Name,Values='Cafe Web Server'" \
  --query 'Reservations[*].Instances[*].SecurityGroups[*].[GroupId]' \
  --region $region --output text)

aws cloudtrail lookup-events \
  --lookup-attributes AttributeKey=ResourceType,AttributeValue=AWS::EC2::SecurityGroup \
  --region $region --output text | grep $sgId
```

> **Screenshot:** Terminal - AWS CLI lookup results filtered to the web server's security group ID

![cloudtrail-cli](screenshots/03-cloudtrail-cli.png)

---

## Task 4 - Analyze Logs with Amazon Athena

### Create Athena table from CloudTrail → Event history → Create Athena table

Selected `monitoring####` bucket → Athena created a `cloudtrail_logs_monitoring####` table with columns matching JSON log structure.

### SQL Queries

**Basic query - explore log structure:**
```sql
SELECT *
FROM cloudtrail_logs_monitoring####
LIMIT 5;
```

**Focused query - key columns only:**
```sql
SELECT useridentity.userName, eventtime, eventsource, eventname, requestparameters
FROM cloudtrail_logs_monitoring####
LIMIT 30;
```

**Challenge - identify the hacker:**
```sql
SELECT DISTINCT useridentity.userName, eventName, eventSource
FROM cloudtrail_logs_monitoring####
WHERE from_iso8601_timestamp(eventtime) > date_add('day', -1, now())
ORDER BY eventSource;
```

```sql
SELECT useridentity.userName, eventtime, eventsource, eventname, requestparameters
FROM cloudtrail_logs_monitoring####
WHERE eventsource = 'ec2.amazonaws.com'
  AND eventname LIKE '%SecurityGroup%';
```

**Finding:** The `chaos` IAM user ran `AuthorizeSecurityGroupIngress` - adding port 22 open to `0.0.0.0/0` - using **programmatic access** from a specific IP address.

> **Screenshot:** Athena query result - chaos user, AuthorizeSecurityGroupIngress event, timestamp, source IP

![athena-hacker-found](screenshots/04-athena-hacker-found.png)

---

## Task 5 - Remove Access + Harden the Environment

### Remove the OS intruder

```bash
# Check recent logins
sudo aureport --auth

# Who is currently logged in?
who    # chaos-user still active!

# Force-kill their session
sudo kill -9 <ProcNum>

# Delete the OS user
sudo userdel -r chaos-user

# Verify no other suspicious users
sudo cat /etc/passwd | grep -v nologin
```

### Fix SSH configuration

```bash
sudo vi /etc/ssh/sshd_config
# Line 61: comment out → # PasswordAuthentication yes
# Line 63: uncomment  → PasswordAuthentication no
sudo service sshd restart
```

Password authentication was enabled - the hacker used username/password to SSH in without a key pair. This has now been disabled.

### Remove unauthorized Security Group rule

Deleted the `0.0.0.0/0` SSH inbound rule from the Web Server security group.

### Restore the website

```bash
cd /var/www/html/cafe/images/
sudo mv Coffee-and-Pastries.backup Coffee-and-Pastries.jpg
```

### Delete the IAM hacker user

**IAM Console → Users → chaos → Delete**

> **Screenshot:** Website restored to normal state

![website-restored](screenshots/05-website-restored.png)

---

## Investigation Summary

| Evidence | Finding |
|---|---|
| CloudTrail event | `AuthorizeSecurityGroupIngress` |
| IAM user | `chaos` |
| Method | Programmatic (AWS CLI) |
| Target | Web Server Security Group |
| Action | Opened port 22 to `0.0.0.0/0` |
| OS access | SSH login using password auth (`chaos-user` OS account) |
| Website damage | Replaced `Coffee-and-Pastries.jpg` with hacked image |

---

## Reflection

In this lab I learned how to:

- **Use CloudTrail as a forensic tool** - every API call in AWS generates a log entry with timestamp, IAM identity, source IP, and parameters; this made it possible to reconstruct exactly what the hacker did, when, and how
- **Analyze logs at scale with Athena SQL** - `WHERE eventsource = 'ec2.amazonaws.com' AND eventname LIKE '%SecurityGroup%'` is far more precise and efficient than grepping through JSON files manually, and scales to millions of log entries
- **Identify the full attack chain** - the hacker used a compromised IAM user (`chaos`) to open port 22 via CLI, then SSH'd in using password authentication with a pre-planted OS user (`chaos-user`), and modified the website from inside the instance
- **Harden the environment after an incident** - removing the IAM user, deleting the OS user, disabling password auth in sshd_config, and restoring the security group are the correct post-incident remediation steps

> Key takeaway: CloudTrail answers "who did what, when, and from where" for every action in an AWS account. Without it, this investigation would have been impossible. Enabling CloudTrail is not optional in production - it is the foundation of security auditing, compliance, and incident response.

---

*Kateryna | AWS Cloud Practitioner Exam Prep*
