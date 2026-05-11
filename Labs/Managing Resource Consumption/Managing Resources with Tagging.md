# Lab: Managing Resources with Tagging

**Course:** AWS Cloud Practitioner Exam Prep  
**Topic:** EC2 Resource Tagging — CLI Filtering, Batch Operations, Tag-or-Terminate Policy  


---

## Overview

Used AWS CLI and PHP scripts to manage 8 EC2 instances based on their tags. Demonstrated how tags enable automated resource operations — from finding and updating instances to stopping entire environments and enforcing compliance policies.

**Tag schema used in this lab:**

| Tag | Values |
|---|---|
| `Project` | `ERPSystem`, `Experiment1` |
| `Environment` | `development`, `staging`, `production` |
| `Version` | `1.0` → updated to `1.1` |

---

## Task 1 — Find and Update Resources by Tag

Connected to **Command Host** EC2 via SSH.

### Progressive CLI filtering with --query (JMESPath)

**Basic filter — all ERPSystem instances:**
```bash
aws ec2 describe-instances \
  --filter "Name=tag:Project,Values=ERPSystem"
```

**Add --query to return only InstanceId:**
```bash
aws ec2 describe-instances \
  --filter "Name=tag:Project,Values=ERPSystem" \
  --query 'Reservations[*].Instances[*].InstanceId'
```

**Return ID + Availability Zone:**
```bash
aws ec2 describe-instances \
  --filter "Name=tag:Project,Values=ERPSystem" \
  --query 'Reservations[*].Instances[*].{ID:InstanceId,AZ:Placement.AvailabilityZone}'
```

**Include tag values in output:**
```bash
aws ec2 describe-instances \
  --filter "Name=tag:Project,Values=ERPSystem" \
  --query 'Reservations[*].Instances[*].{
    ID:InstanceId,
    AZ:Placement.AvailabilityZone,
    Project:Tags[?Key==`Project`] | [0].Value,
    Environment:Tags[?Key==`Environment`] | [0].Value,
    Version:Tags[?Key==`Version`] | [0].Value
  }'
```

**Filter by two tags — ERPSystem + development only:**
```bash
aws ec2 describe-instances \
  --filter "Name=tag:Project,Values=ERPSystem" \
          "Name=tag:Environment,Values=development" \
  --query 'Reservations[*].Instances[*].{
    ID:InstanceId,
    Environment:Tags[?Key==`Environment`] | [0].Value,
    Version:Tags[?Key==`Version`] | [0].Value
  }'
```

Returns only **2 instances** — ERPSystem + development.

> **Screenshot:** Terminal — filtered output showing 2 development instances with tags

![filtered-instances](screenshots/01-filtered-instances.png)

### Batch update Version tag with a shell script

```bash
# change-resource-tags.sh
ids=$(aws ec2 describe-instances \
  --filter "Name=tag:Project,Values=ERPSystem" \
           "Name=tag:Environment,Values=development" \
  --query 'Reservations[*].Instances[*].InstanceId' \
  --output text)

aws ec2 create-tags --resources $ids --tags 'Key=Version,Value=1.1'
```

```bash
./change-resource-tags.sh
```

Verified: development instances now show `Version: 1.1`, production instances unchanged at `1.0`.

> **Screenshot:** Terminal — all ERPSystem instances showing updated Version values

![version-updated](screenshots/02-version-updated.png)

---

## Task 2 — Stop and Restart Instances by Tag (Stopinator)

Used the pre-provided `stopinator.php` script (PHP + AWS SDK) to manage environments by tag:

**Stop all ERPSystem development instances:**
```bash
./stopinator.php -t"Project=ERPSystem;Environment=development"
```

Script scans **every AWS region**, identifies matching instances, and stops them simultaneously.

**Restart the same instances:**
```bash
./stopinator.php -t"Project=ERPSystem;Environment=development" -s
```

Verified in EC2 Console: 2 instances stopped → then restarted.

> **Screenshot:** EC2 Console → 2 development instances in **Stopped** state after stopinator ran

![instances-stopped](screenshots/03-instances-stopped.png)

---

## Task 3 — Challenge: Tag-or-Terminate Policy

**Policy:** Any EC2 instance in the private subnet without an `Environment` tag must be terminated.

### Setup

Removed the `Environment` tag from 2 instances in the EC2 Console → Tags tab → Edit → delete `Environment` → Save.

### Run the terminate-instances.php script

```bash
./terminate-instances.php -region <region> -subnetid <subnet-id>
```

**Script logic:**
1. Find all instances that have `Environment` tag (compliant set)
2. Find all instances in the subnet
3. Any instance NOT in the compliant set → terminate

**Output:**
```
Checking i-xxxxxxxx ... (compliant)
Checking i-yyyyyyyy ... (no Environment tag → terminate)
Terminating instances...
Instances terminated.
```

> **Screenshot:** Terminal — script output showing which instances were checked and terminated

![terminate-output](screenshots/04-terminate-output.png)

---

## Key Patterns Demonstrated

| Pattern | Command / Tool |
|---|---|
| Filter by tag | `--filter "Name=tag:Key,Values=Value"` |
| Multi-tag filter | Multiple `--filter` arguments |
| Extract tag value in query | `Tags[?Key==\`Project\`] \| [0].Value` |
| Batch tag update | `aws ec2 create-tags --resources $ids` |
| Stop by tag (multi-region) | `stopinator.php -t"Key=Value"` |
| Terminate non-compliant | `terminate-instances.php` + `terminateInstances()` |

---

## Reflection

In this lab I learned how to:

- **Use JMESPath queries to extract structured data from CLI output** — building from simple InstanceId lists to multi-field objects with tag values shows how `--query` transforms raw JSON into actionable data for scripting and automation
- **Batch update tags with a shell script** — combining `describe-instances --output text` to get IDs and piping them to `create-tags` is the correct pattern for bulk tag management across many resources
- **Use the Stopinator pattern for cost control** — stopping all `development` instances at end of day and restarting them next morning (triggered by tag) is a real production practice for reducing AWS costs by 60–70% on non-production environments
- **Implement a tag-or-terminate compliance policy** — set subtraction (all instances minus tagged instances = non-compliant instances) is the algorithmic core of automated governance, which maps directly to AWS Config rules and Lambda-based enforcement in production

> Key takeaway: Tags are not just labels — they are the primary mechanism for automated resource management in AWS. Every operational policy (cost allocation, environment control, compliance enforcement) is implemented by filtering and acting on tags. Mastering tag-based CLI operations is essential for any cloud operations role.

---

*Kateryna | AWS Cloud Practitioner Exam Prep*
