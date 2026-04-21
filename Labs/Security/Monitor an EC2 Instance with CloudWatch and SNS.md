# Lab Monitor an EC2 Instance with CloudWatch and SNS

> **Course:** AWS Cloud Practitioner (CLF-C02)  
> **Topic:** CloudWatch Alarms, SNS Notifications, CPU Stress Test, Dashboard  
---

## Lab Objective

Set up automated monitoring for an EC2 instance: create a CloudWatch alarm that triggers an SNS email notification when CPU utilization exceeds 60% — then simulate a real threat by stress-testing the instance to 100% CPU and confirm the alert fires.

---

## Tasks Completed

### ✅ Task 1 — Created an SNS Topic and Email Subscription
- Created SNS topic `MyCwAlarm` (Standard type)
- Subscribed an email address → confirmed subscription via inbox link
- Status changed from **Pending → Confirmed**
  <img width="1920" height="1040" alt="subscription" src="https://github.com/user-attachments/assets/5af822f5-f9f2-4611-b036-bc5447114c00" />
  <img width="1366" height="728" alt="subscription-confirmed" src="https://github.com/user-attachments/assets/cac2099f-b63b-4cd2-8a38-72677bf7cc47" />

### ✅ Task 2 — Created a CloudWatch Alarm
- Navigated to **CloudWatch → Metrics → EC2 → Per-Instance Metrics**
- Selected `CPUUtilization` for the `Stress Test` instance
- Created alarm `LabCPUUtilizationAlarm`:
  - Metric: Average CPU utilization
  - Period: **1 minute**
  - Threshold: **> 60%** → state: **In alarm**
  - Action: publish to `MyCwAlarm` SNS topic
    <img width="1366" height="728" alt="Created a CloudWatch Alarm" src="https://github.com/user-attachments/assets/22c7e7db-0c3d-471b-a122-401139c2fb6c" />


### ✅ Task 3 — Stress Tested the Instance
- Connected to the `Stress Test` EC2 instance via the provided URL
- Ran the stress command:

```bash
sudo stress --cpu 10 -v --timeout 400s
```
<img width="1920" height="1040" alt="stressed" src="https://github.com/user-attachments/assets/3fd366ed-90a7-41b4-89a5-13336579bf94" />

- Opened a second terminal session and ran `top` to observe live CPU usage at 100%
- Monitored CloudWatch alarm — status changed to **In alarm**
  <img width="1920" height="1040" alt="stressed-1" src="https://github.com/user-attachments/assets/5a39102a-919d-435e-9d83-535d43a85ead" />

- Received SNS email notification: **AWS Notifications — LabCPUUtilizationAlarm**
  <img width="1920" height="1040" alt="notification" src="https://github.com/user-attachments/assets/dbe0f466-30ea-4b8e-9915-6221684c7d80" />


### ✅ Task 4 — Created a CloudWatch Dashboard
- Created dashboard `LabEC2Dashboard`
- Added a **Line widget** with `CPUUtilization` metric for the Stress Test instance
- Saved the dashboard for ongoing monitoring access
<img width="1920" height="1040" alt="Dashboard" src="https://github.com/user-attachments/assets/37949002-34a7-4852-854b-6f2ffdd6a96d" />


---

## Key Takeaways

| Concept | What I Learned |
|---------|----------------|
| **CloudWatch Alarm** | Watches a single metric and triggers an action when it crosses a threshold — fully automated, no manual checking needed |
| **Alarm States** | Three states: `OK`, `In alarm`, `Insufficient data` — each can trigger a different action |
| **SNS + CloudWatch** | CloudWatch publishes to an SNS topic; SNS delivers to subscribers — clean separation of detection and notification |
| **1-minute period** | Granular monitoring — alarms respond faster when metrics are evaluated every minute vs every 5 minutes |
| **Stress test as security simulation** | CPU spike = one indicator of malware or cryptomining — monitoring catches what logs alone might miss |
| **CloudWatch Dashboard** | Centralizes key metrics in one view — useful for ongoing operational visibility without digging through menus |

---

## My Reflection

This lab connected monitoring to a real-world threat scenario, which made it significantly more memorable than a standard configuration exercise. Running `sudo stress --cpu 10` and watching the CPU hit 100% in `top` — then seeing the CloudWatch alarm flip to **In alarm** and an email arrive minutes later — made the entire monitoring pipeline feel alive.

The most important conceptual shift was understanding that **monitoring is not passive**. CloudWatch does not just record data — it evaluates it against rules and acts. The alarm → SNS → email chain is an automated response system that works without anyone watching a dashboard.

The 1-minute evaluation period was also a meaningful detail. The default 5-minute period would have delayed the alert significantly. In a real incident, those 4 minutes could matter — especially if the CPU spike represents an active intrusion or runaway process.

> 💡 **Key Insight:** You cannot respond to what you do not detect. CloudWatch alarms are the detection layer — they turn raw metrics into actionable signals. Without them, an EC2 instance running at 100% CPU for hours might go completely unnoticed.

---

*Author: Kateryna | AWS Cloud Practitioner Exam Prep*
