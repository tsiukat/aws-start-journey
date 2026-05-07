# Lab: Amazon Route 53 Failover Routing

**Course:** AWS Cloud Practitioner Exam Prep  
**Topic:** Route 53 — Health Checks, Failover Routing Policy, SNS Alerts  
**Duration:** ~45 min

---

## Overview

Configured DNS-level automatic failover for a café web application running on two EC2 instances in different Availability Zones. If the primary instance fails, Route 53 detects it via a health check and redirects traffic to the secondary instance — automatically, without manual intervention.

**Final architecture:**

```
User request → Route 53 (www.domain.vocareum.training)
                    │
              Health check OK?
             ┌──────┴──────┐
            YES             NO → SNS email alert
             │                        │
      CafeInstance1           CafeInstance2
      (AZ1 - Primary)         (AZ2 - Secondary)
```

---

## Task 1 — Confirm Both Café Websites Are Running

Two EC2 instances pre-created by CloudFormation:

| Instance | Subnet | Availability Zone | Role |
|---|---|---|---|
| CafeInstance1 | Cafe Public Subnet 1 | us-west-2a | Primary |
| CafeInstance2 | Cafe Public Subnet 2 | us-west-2b | Secondary |

Verified both instances serve the café application by opening `PrimaryWebSiteURL` and `SecondaryWebsiteURL` in browser — both displayed instance metadata confirming different AZs.

> **Screenshot:** Browser — PrimaryWebSiteURL showing CafeInstance1 AZ
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/0f690346-d817-40a9-8819-96b70c183a97" />
>
> **Screenshot:** Browser — PrimaryWebSiteURL showing CafeInstance2 AZ
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/94ea97cf-0061-4a7f-9810-80726ed70ac5" />

---

## Task 2 — Configure Route 53 Health Check

In **Route 53 Console → Health checks → Create health check**:

| Setting | Value |
|---|---|
| Name | `Primary-Website-Health` |
| Monitor | Endpoint (by IP address) |
| IP address | CafeInstance1 public IPv4 |
| Path | `cafe` |
| Request interval | Fast (10 seconds) |
| Failure threshold | 2 |
| Alarm | Yes → new SNS topic |
| SNS topic name | `Primary-Website-Health` |
| Notification email | personal email address |

Confirmed subscription via email link from AWS Notifications.

> **Screenshot:** Route 53 → Health checks → `Primary-Website-Health` status = **Healthy** + SNS created
<img width="1920" height="1040" alt="image" src="https://github.com/user-attachments/assets/24e2237d-a275-4b67-a81a-10b5a2ed5bd4" />



---

## Task 3 — Configure Route 53 Failover DNS Records

In **Route 53 → Hosted zones → domain.vocareum.training → Create record**:

### Primary A Record

| Setting | Value |
|---|---|
| Record name | `www` |
| Record type | A (IPv4) |
| Value | CafeInstance1 IP |
| TTL | 15 seconds |
| Routing policy | **Failover** |
| Failover type | **Primary** |
| Health check | Primary-Website-Health |
| Record ID | `FailoverPrimary` |

### Secondary A Record

| Setting | Value |
|---|---|
| Record name | `www` |
| Record type | A (IPv4) |
| Value | CafeInstance2 IP |
| TTL | 15 seconds |
| Routing policy | **Failover** |
| Failover type | **Secondary** |
| Health check | (none) |
| Record ID | `FailoverSecondary` |

> **Screenshot:** Route 53 → Hosted zone — both A records (Primary + Secondary) listed

![dns-records](screenshots/03-dns-records.png)

---

## Task 4 — Verify DNS Resolution

Opened `http://www.domain.vocareum.training/cafe/` in browser → café site loaded, Server Information confirmed **us-west-2a** (CafeInstance1 = Primary).

> **Screenshot:** Browser — café site via Route 53 DNS showing AZ = us-west-2a

![dns-verified](screenshots/04-dns-verified.png)

---

## Task 5 — Simulate Failure and Verify Failover

**Stopped CafeInstance1** via EC2 Console → Instance state → Stop instance.

Monitored in **Route 53 → Health checks → Primary-Website-Health → Monitoring tab** until status changed to **Unhealthy** (within ~2 minutes with Fast/10s interval).

Refreshed the browser tab with `www.domain.vocareum.training/cafe/` → page now served from **us-west-2b** (CafeInstance2 = Secondary). ✅

Received SNS email: **"ALARM: Primary-Website-Health-awsroute53-..."**

> **Screenshot:** Route 53 → Health check → Monitoring tab showing **Unhealthy** status

![health-check-unhealthy](screenshots/05-health-check-unhealthy.png)

> **Screenshot:** Browser — same URL now showing AZ = us-west-2b (failover active)

![failover-confirmed](screenshots/06-failover-confirmed.png)

> **Screenshot:** Email — SNS alarm notification for primary health check failure

![sns-alarm-email](screenshots/07-sns-alarm-email.png)

---

## Route 53 Routing Policies — Context

| Policy | Use case |
|---|---|
| **Simple** | Single resource, no health checks |
| **Failover** | Active-passive HA — primary + standby |
| **Weighted** | A/B testing, gradual traffic shifting |
| **Latency** | Route to lowest-latency region |
| **Geolocation** | Route based on user's country/region |

This lab used **Failover** — the core pattern for disaster recovery and high availability at the DNS layer.

---

## Reflection

In this lab I learned how to:

- **Configure DNS-level failover** with Route 53 — understanding that health checks operate independently from the application, polling the endpoint every 10 seconds and triggering DNS record changes automatically when thresholds are crossed
- **Distinguish TTL impact on failover speed** — setting TTL to 15 seconds means DNS caches expire quickly, so clients receive the updated (secondary) IP address within seconds of failover instead of waiting hours
- **Use SNS for operational alerting** — the health check alarm sent an email the moment the primary instance became unreachable, demonstrating how AWS services compose together for observable, automated operations
- **Observe the full failover lifecycle** in real time — stopping an instance, watching the health check turn unhealthy, refreshing the browser, and seeing a different AZ in the Server Information field made the concept concrete

> Key takeaway: Route 53 failover routing is the DNS layer of high availability — it works above the infrastructure layer (unlike ELB, which operates within a region). Together, ELB + Auto Scaling + Route 53 failover cover availability at every level: instance, AZ, and region.

---

*Kateryna | AWS Cloud Practitioner Exam Prep*
