# Lab: Monitoring Infrastructure

**Course:** AWS Cloud Practitioner Exam Prep  
**Topic:** CloudWatch Metrics, Logs, Alarms, Events + AWS Config Compliance  
---

## Overview

Configured a full monitoring stack for an EC2 web server using AWS-native observability tools:

| Tool | Purpose |
|---|---|
| **CloudWatch Agent** | Collects OS-level metrics + app logs from inside the instance |
| **CloudWatch Logs** | Stores and filters web server access/error logs |
| **CloudWatch Metrics** | Graphs CPU, memory, disk usage over time |
| **CloudWatch Alarms** | Triggers SNS notification when 404 errors exceed threshold |
| **CloudWatch Events** | Real-time alerts on EC2 state changes (stop/terminate) |
| **AWS Config** | Continuous compliance auditing (tags, EBS attachment) |

---

## Task 1 — Install CloudWatch Agent via Systems Manager

### Step 1: Install agent using Run Command

In **Systems Manager → Run Command → AWS-ConfigureAWSPackage**:

| Setting | Value |
|---|---|
| Action | Install |
| Name | `AmazonCloudWatchAgent` |
| Version | latest |
| Target | Web Server (instance) |

Verified: **Overall status = Success** → "Successfully installed AmazonCloudWatchAgent"

### Step 2: Create agent config in Parameter Store

In **Systems Manager → Parameter Store → Create parameter**:

| Setting | Value |
|---|---|
| Name | `Monitor-Web-Server` |
| Description | Collect web logs and system metrics |

Config defines:
- **Logs**: `/var/log/httpd/access_log` → `HttpAccessLog` log group, `/var/log/httpd/error_log` → `HttpErrorLog`
- **Metrics**: CPU (idle/iowait/user/system), disk (used%, inodes), disk I/O, memory (mem_used%), swap — every **10 seconds**

### Step 3: Start agent using Run Command

Run **AmazonCloudWatch-ManageAgent** with:
- Action: `configure` | Mode: `ec2` | Config Source: `ssm` | Location: `Monitor-Web-Server`

> **Screenshot:** Systems Manager → Run Command → Overall status = **Success**

![agent-installed](screenshots/01-agent-installed.png)

---

## Task 2 — Monitor Application Logs (CloudWatch Logs)

Generated log data by hitting a non-existent page: `http://WebServerIP/start` → 404 response.

In **CloudWatch → Log groups → HttpAccessLog** → confirmed 404 entries in the log stream.

### Create Metric Filter for 404 Errors

Filter pattern:
```
[ip, id, user, timestamp, request, status_code=404, size]
```

| Setting | Value |
|---|---|
| Filter name | `404Errors` |
| Metric namespace | `LogMetrics` |
| Metric name | `404Errors` |
| Metric value | `1` |

### Create Alarm on the Metric

| Setting | Value |
|---|---|
| Alarm name | `404 Errors` |
| Condition | `≥ 5` in 1 minute |
| Action | SNS email notification |

Triggered alarm by hitting 5+ non-existent URLs → alarm turned **red** → received email alert. ✅

> **Screenshot:** CloudWatch → HttpAccessLog stream showing 404 entries

![log-404](screenshots/02-log-404.png)

> **Screenshot:** CloudWatch → `404 Errors` alarm in **ALARM** state (red)

![alarm-triggered](screenshots/03-alarm-triggered.png)

---

## Task 3 — Monitor Instance Metrics (CloudWatch Metrics)

In **CloudWatch → Metrics → All metrics → CWAgent**:

- `CWAgent → device, fstype, host, path` → **disk space** metrics
- `CWAgent → host` → **memory (mem_used_percent)** metrics

Standard EC2 metrics (CPU, network) show the instance from outside. CloudWatch Agent metrics show what's happening **inside** — memory and disk are not available without the agent.

> **Screenshot:** CloudWatch → CWAgent metrics — mem_used_percent graph

![cwagent-metrics](screenshots/04-cwagent-metrics.png)

---

## Task 4 — Real-Time Notifications with CloudWatch Events

In **CloudWatch → Events → Rules → Create rule**:

| Setting | Value |
|---|---|
| Rule name | `Instance_Stopped_Terminated` |
| Event source | EC2 |
| Event type | EC2 Instance State-change Notification |
| States | `stopped` + `terminated` |
| Target | SNS topic (email subscription) |

**Test:** Stopped the Web Server EC2 instance → received JSON-formatted email notification within ~1 minute. ✅

> **Screenshot:** Email — CloudWatch Events SNS notification after instance stopped

![events-notification](screenshots/05-events-notification.png)

---

## Task 5 — Infrastructure Compliance with AWS Config

Added two managed rules:

### Rule 1: `required-tags`

| Setting | Value |
|---|---|
| tag1Key | `project` |
| Result | Web Server EC2 = **Compliant** (has `project` tag) |
| Non-compliant | Many other resources without the tag |

### Rule 2: `ec2-volume-inuse-check`

| Result | Resource |
|---|---|
| Compliant | EBS volume attached to an instance |
| Non-compliant | EBS volume NOT attached to any instance |

AWS Config continuously re-evaluates rules — any new resource without a `project` tag is flagged automatically.

> **Screenshot:** AWS Config → Rules — both rules showing Compliant/Non-compliant counts

![config-rules](screenshots/06-config-rules.png)

---

## Monitoring Stack Summary

```
EC2 Web Server
    │
    ├── CloudWatch Agent → OS metrics (CPU, mem, disk) → CloudWatch Metrics
    ├── CloudWatch Agent → /var/log/httpd/* → CloudWatch Logs
    │                           │
    │                    Metric Filter (404s)
    │                           │
    │                    CloudWatch Alarm → SNS email
    │
    ├── EC2 State Changes → CloudWatch Events → SNS email
    │
    └── Tags / EBS config → AWS Config Rules → Compliance report
```

---

## Reflection

In this lab I learned how to:

- **Install and configure the CloudWatch Agent** via Systems Manager Run Command — understanding that standard CloudWatch cannot see inside an EC2 instance; the agent bridges that gap by collecting memory, disk, and application logs from within
- **Build a log-based alerting pipeline** — CloudWatch Logs → Metric Filter → CloudWatch Alarm → SNS; this pattern converts raw log lines into operational alerts without any code changes to the application
- **Use CloudWatch Events for real-time infrastructure awareness** — receiving a JSON notification within seconds of stopping an EC2 instance showed how AWS can react to state changes programmatically
- **Apply AWS Config for continuous compliance** — the `required-tags` and `ec2-volume-inuse-check` rules demonstrate that compliance is not a one-time audit but an ongoing automated check that flags new violations as they appear

> Key takeaway: Monitoring in AWS is a multi-layer discipline — metrics for performance, logs for behavior, alarms for thresholds, events for state changes, and Config for compliance. Understanding which tool addresses which concern is essential for both operating production systems and for the CLF-C02 exam.

---

*Kateryna | AWS Cloud Practitioner Exam Prep*
