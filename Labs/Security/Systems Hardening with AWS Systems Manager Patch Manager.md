# Lab 11 — Systems Hardening with AWS Systems Manager Patch Manager

> **Course:** AWS Cloud Practitioner (CLF-C02)  
> **Topic:** Patch Manager, Patch Baselines, Patch Groups, Compliance  
> **Date:** <!-- add date -->

---

## Lab Objective

Use AWS Systems Manager Patch Manager to patch Linux instances using a default baseline and Windows instances using a custom-built baseline — then verify full compliance across all 6 EC2 instances.

---

## Environment

| Instances | OS | Patch Group Tag |
|-----------|-----|----------------|
| Linux-1, Linux-2, Linux-3 | Amazon Linux 2 | `LinuxProd` |
| Windows-1, Windows-2, Windows-3 | Windows Server 2019 | `WindowsProd` |

---

## Tasks Completed

### ✅ Task 1 — Patched Linux Instances Using Default Baseline
- Used **Patch Manager → Patch now**
- Operation: **Scan and Install**, target by tag: `Patch Group = LinuxProd`
- Applied baseline: `AWS-AmazonLinux2DefaultPatchBaseline`
- Monitored until all 3 Linux instances completed successfully

### ✅ Task 2 — Created a Custom Patch Baseline for Windows
- Created baseline `WindowsServerSecurityUpdates` for Windows Server 2019
- Added two approval rules:
  - **Critical** SecurityUpdates → auto-approve after 3 days → compliance: Critical
  - **Important** SecurityUpdates → auto-approve after 3 days → compliance: High
- Associated the baseline with patch group: **WindowsProd**

### ✅ Task 3 — Tagged and Patched Windows Instances
- Added tag `Patch Group = WindowsProd` to Windows-1, Windows-2, Windows-3
- Ran **Patch now** targeting tag `Patch Group = WindowsProd`
- Patch Manager used **Run Command → RunPatchBaseline** behind the scenes
- Monitored execution output — confirmed `PatchGroup: WindowsProd` in logs

### ✅ Task 4 — Verified Compliance
- **Compliance summary: 6 Compliant** (3 Linux + 3 Windows)
- Reviewed per-instance details: critical/security/other noncompliant counts, last operation date, baseline ID
- Confirmed patch list and install timestamps on individual Windows node

---

## Screenshots

### Task 1 — Linux Patching Complete

<!-- Add screenshot: Patch now status panel showing 3 Linux instances completed -->
![Linux Patching](screenshots/task1-linux-patching-complete.png)

---

### Task 2 — Custom Windows Baseline Created

<!-- Add screenshot: WindowsServerSecurityUpdates baseline page with approval rules visible -->
![Custom Baseline](screenshots/task2-custom-baseline.png)

---

### Task 3 — Windows Patching — Run Command Output

<!-- Add screenshot: Run Command output showing PatchGroup: WindowsProd in execution log -->
![Windows Patching](screenshots/task3-windows-patching-output.png)

---

### Task 4 — Compliance Summary: 6 Compliant

<!-- Add screenshot: Patch Manager Dashboard showing Compliant: 6 -->
![Compliance Dashboard](screenshots/task4-compliance-summary.png)

---

### Task 4 — Per-Instance Patch Details

<!-- Add screenshot: Patch tab for one Windows node showing applied patches and install timestamps -->
![Patch Details](screenshots/task4-patch-details.png)

---

## Key Takeaways

| Concept | What I Learned |
|---------|----------------|
| **Patch Manager** | Automates OS and application patching across EC2 instances at scale — no manual SSH required |
| **Default vs Custom Baseline** | Default baselines work out of the box; custom baselines give control over which patch classifications and severities are applied |
| **Patch Groups** | EC2 instances are targeted via tags (`Patch Group = LinuxProd`) — clean separation between environments |
| **Auto-approval delay** | A 3-day delay before patches are auto-approved gives time to test stability before wide deployment |
| **Run Command under the hood** | Patch Manager calls `RunPatchBaseline` via Run Command — the same mechanism can be scripted independently |
| **Compliance Reporting** | Patch Manager tracks compliant/noncompliant status per instance — visible across the entire fleet in one view |

---

## My Reflection

This lab addressed a problem that sounds simple but is genuinely hard at scale: keeping hundreds of servers patched without logging into each one manually. Patch Manager solves this by treating instances as a fleet, not as individual machines — you define a policy, tag your targets, and the system does the rest.

The difference between **default and custom baselines** was the most instructive part. The Linux default baseline is ready to use immediately — good for standard environments. The Windows custom baseline required deliberate choices: which OS version, which classifications (SecurityUpdates only), which severities (Critical and Important), and how long to wait before auto-approving. Those decisions reflect a real security policy, not just a configuration screen.

The **3-day auto-approval delay** was a detail that stuck with me. In production, you do not want patches applied the moment they are released — you want time to test them. That delay is a controlled window between "available" and "applied," and it is the kind of operational nuance that separates a thoughtful patching policy from a reactive one.

> 💡 **Key Insight:** Patch compliance is not a one-time task — it is a continuous state that needs to be defined, enforced, and monitored. Patch Manager automates the enforcement; the administrator still needs to design the policy that makes the enforcement meaningful.

---

*Author: Kateryna | AWS Cloud Practitioner Exam Prep*
