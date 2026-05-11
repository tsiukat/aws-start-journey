# Lab: Troubleshoot AWS CloudFormation

**Course:** AWS Cloud Practitioner Exam Prep  
**Topic:** Infrastructure as Code - CloudFormation Debugging, Drift Detection, Stack Lifecycle  

---

## Overview

Practiced the full CloudFormation troubleshooting workflow: a stack that fails silently, a stack that fails with `DO_NOTHING` so logs can be inspected, a template fix, drift detection after a manual resource change, and a failed delete that required the `--retain-resources` pattern.

**Resources created by the stack:**
- Custom VPC + public subnet
- EC2 web server with UserData bootstrap script
- S3 bucket
- Security Group (`WebServerSG`)
- WaitCondition + WaitHandle (deployment signal)

---

## Task 1 - JMESPath Practice

Practiced JMESPath queries at [jmespath.org](https://jmespath.org) before running CLI commands.

Key syntax learned:

| Expression | Meaning |
|---|---|
| `desserts[1]` | Second element of an array |
| `desserts[0].name` | Attribute of a specific element |
| `desserts[].name` | Attribute across all elements |
| `desserts[?name=='Carrot cake']` | Filter by value |
| `StackResources[?ResourceType=='AWS::EC2::Instance'].LogicalResourceId` | Filter CloudFormation resources by type |

Applied directly in all subsequent `--query` parameters throughout the lab.

---

## Task 2 - Troubleshoot Failed CloudFormation Stack

### Attempt 1 - Stack creates, then rolls back

```bash
aws cloudformation create-stack \
  --stack-name myStack \
  --template-body file://template1.yaml \
  --capabilities CAPABILITY_NAMED_IAM \
  --parameters ParameterKey=KeyName,ParameterValue=vockey
```

Monitored with:
```bash
watch -n 5 -d \
  aws cloudformation describe-stack-resources \
  --stack-name myStack \
  --query 'StackResources[*].[ResourceType,ResourceStatus]' \
  --output table
```

Resources created → then all **deleted**. Stack status: `ROLLBACK_COMPLETE`.

Analyzed the failure:
```bash
aws cloudformation describe-stack-events \
  --stack-name myStack \
  --query "StackEvents[?ResourceStatus == 'CREATE_FAILED']"
```

**Finding:** `WaitCondition timed out` - UserData script never sent a success signal.

---

### Attempt 2 - `DO_NOTHING` flag to preserve logs

```bash
aws cloudformation create-stack \
  --stack-name myStack \
  --template-body file://template1.yaml \
  --capabilities CAPABILITY_NAMED_IAM \
  --on-failure DO_NOTHING \
  --parameters ParameterKey=KeyName,ParameterValue=vockey
```

Stack status: `CREATE_FAILED` - but EC2 instance **not deleted** this time.

SSH'd into the Web Server instance and inspected logs:

```bash
tail -50 /var/log/cloud-init-output.log
```

**Root cause found:**
```
No package http available.
util.py[WARNING]: Failed running /var/lib/cloud/instance/scripts/part-001
```

The UserData script tried to install `http` instead of `httpd` (the correct Apache package name on Amazon Linux). Because the script used `#!/bin/bash -e`, any failed command immediately stopped the whole script - so the WaitCondition never received its success signal.

> **Screenshot:** Terminal - `cloud-init-output.log` showing the "No package http available" error

![cloudinit-error](screenshots/01-cloudinit-error.png)

---

### Fix - Correct the template and redeploy

```bash
vim template1.yaml
# Line 128: changed "http" → "httpd"
```

```bash
aws cloudformation delete-stack --stack-name myStack

aws cloudformation create-stack \
  --stack-name myStack \
  --template-body file://template1.yaml \
  --capabilities CAPABILITY_NAMED_IAM \
  --on-failure DO_NOTHING \
  --parameters ParameterKey=KeyName,ParameterValue=vockey
```

Stack status: **`CREATE_COMPLETE`** ✅

Tested web server: opened `http://<PublicIP>` → "Hello from your web server!"

> **Screenshot:** Terminal - stack resources all showing `CREATE_COMPLETE`

![stack-complete](screenshots/02-stack-complete.png)

> **Screenshot:** Browser - web server responding with Hello message

![webserver-hello](screenshots/03-webserver-hello.png)

---

## Task 3 - Detect Configuration Drift

### Manually modified the Security Group (outside CloudFormation)

In EC2 Console → Web Server → Security tab → `WebServerSG` → Inbound rules → Changed SSH source from `0.0.0.0/0` to **My IP**.

### Added file to S3 bucket (outside CloudFormation)

```bash
bucketName=$(aws cloudformation describe-stacks \
  --stack-name myStack \
  --query "Stacks[*].Outputs[?OutputKey=='BucketName'].[OutputValue]" \
  --output text)

touch myfile
aws s3 cp myfile s3://$bucketName/
```

### Run drift detection

```bash
aws cloudformation detect-stack-drift --stack-name myStack

# Check drift status
aws cloudformation describe-stack-resource-drifts \
  --stack-name myStack \
  --stack-resource-drift-status-filters MODIFIED
```

**Results via JMESPath table query:**

```bash
aws cloudformation describe-stack-resources \
  --stack-name myStack \
  --query 'StackResources[*].[ResourceType,ResourceStatus,DriftInformation.StackResourceDriftStatus]' \
  --output table
```

| Resource | Drift Status |
|---|---|
| Security Group | **MODIFIED** - SSH source changed from `0.0.0.0/0` to My IP |
| S3 Bucket | **IN_SYNC** - adding files does not register as drift |
| Other resources | IN_SYNC |

> **Screenshot:** Terminal - drift detection table showing MODIFIED for Security Group

![drift-detected](screenshots/04-drift-detected.png)

---

## Task 4 - Challenge: Delete Stack While Keeping S3 Bucket

### First delete attempt - fails

```bash
aws cloudformation delete-stack --stack-name myStack
# StackStatus: DELETE_FAILED
# Reason: bucket contains objects → CloudFormation refuses to delete it
```

### Solution - `--retain-resources` flag

```bash
# Get the logical resource ID of the bucket
aws cloudformation describe-stack-resources \
  --stack-name myStack \
  --query 'StackResources[?ResourceType==`AWS::S3::Bucket`].LogicalResourceId' \
  --output text

# Delete stack, retaining the bucket
aws cloudformation delete-stack \
  --stack-name myStack \
  --retain-resources MyBucket
```

Stack status: **`DELETE_COMPLETE`** ✅  
S3 bucket and its contents: **preserved** ✅

> **Screenshot:** Terminal - `DELETE_COMPLETE` status after retain-resources solution

![delete-complete](screenshots/05-delete-complete.png)

---

## Key Troubleshooting Patterns

| Situation | Solution |
|---|---|
| Stack rolls back silently | Use `--on-failure DO_NOTHING` to preserve resources for inspection |
| UserData script fails | Check `/var/log/cloud-init-output.log` on the instance |
| WaitCondition timeout | Script never completed → find the error in cloud-init logs |
| Resource was manually changed | Run `detect-stack-drift` → check `MODIFIED` resources |
| Stack delete fails (non-empty bucket) | Use `--retain-resources <LogicalId>` to exclude it from deletion |

---

## Reflection

In this lab I learned how to:

- **Debug CloudFormation failures using `--on-failure DO_NOTHING`** - the default rollback behavior deletes evidence; preserving the failed stack lets you SSH into the instance and read cloud-init logs to find the actual error
- **Understand the WaitCondition pattern** - it creates a deployment contract: the UserData script must signal success within the timeout window; any script failure with `#!/bin/bash -e` breaks the chain and causes the condition to time out
- **Use drift detection as an audit tool** - `detect-stack-drift` reveals the gap between what CloudFormation deployed and what currently exists; only property changes (not data changes like S3 uploads) register as drift
- **Use `--retain-resources` for safe stack deletion** - when a bucket has data that must be preserved, excluding it from deletion allows the rest of the stack to be cleaned up cleanly, which is the correct production pattern

> Key takeaway: CloudFormation is not just a deployment tool - it is a continuous contract between your template and your infrastructure. Drift detection enforces that contract, `--on-failure DO_NOTHING` makes failures debuggable, and `--retain-resources` makes cleanup safe. Mastering these patterns is what separates infrastructure automation from fragile one-time deployments.

---

*Kateryna | AWS Cloud Practitioner Exam Prep*
