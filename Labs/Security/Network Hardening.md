
# Lab 10 — Vulnerability Assessment with Amazon Inspector

> **Course:** AWS Cloud Practitioner (CLF-C02)  
> **Topic:** Amazon Inspector, CVE, Lambda Security, Vulnerability Remediation  

---

## Lab Objective

Activate Amazon Inspector to automatically scan Lambda functions for known vulnerabilities, interpret the findings using CVE data, and remediate by updating the vulnerable package.

---

## Tasks Completed

### ✅ Task 1 — Activated Amazon Inspector
- Enabled Inspector for the AWS account — automatically scans EC2, ECR, and Lambda functions
- Waited for **Environment coverage → Lambda functions: 100%** on the dashboard

### ✅ Task 2 — Reviewed Vulnerability Findings
- Navigated to **Findings → All findings**
- Found **3 vulnerabilities** across Lambda functions, all severity: **Medium**
- Reviewed finding `CVE-2023-32681` affecting the `requests` Python package in the `get-request` Lambda function
- Opened the vulnerability detail in the **National Vulnerability Database (NVD)** — confirmed the issue is an outdated `requests` package
- Remediation recommendation: **upgrade to the latest version**

### ✅ Task 3 — Remediated the Vulnerability
- Opened the `get-request` Lambda function in the console
- Edited `requirements.txt`: changed `requests==2.20.0` → `requests` (no pinned version = always installs latest)
- Deployed the updated function → Inspector automatically triggered a new scan
- In Inspector findings, changed filter to **Closed** — confirmed `CVE-2023-32681` now shows as **Closed**
- Verified the Lambda function's **Last scanned** timestamp was updated

---

## Screenshots

### Inspector Dashboard — Lambda Coverage 100%

<img width="1920" height="1040" alt="Inspector Dashboard — Lambda Coverage 100%" src="https://github.com/user-attachments/assets/a689d2f2-9865-4a18-b051-5830dd91b9b1" />

---

### All Findings — CVE List & CVE-2023-32681 Detail Panel

<img width="1920" height="1040" alt="All Findings — CVE List" src="https://github.com/user-attachments/assets/6c6598eb-7c2f-4fbe-998d-14b539f2002f" />


---

### Finding Status — Closed After Remediation

<img width="1920" height="1040" alt="Finding Status — Closed After Remediation" src="https://github.com/user-attachments/assets/7c398e56-2c70-4d91-bb98-601788bf8b55" />

---

## Key Takeaways

| Concept | What I Learned |
|---------|----------------|
| **Amazon Inspector** | Automated security scanning for Lambda, EC2, and ECR — activates once and scans continuously |
| **CVE (Common Vulnerabilities and Exposures)** | A standardized ID system for known security vulnerabilities — links directly to NVD for full details |
| **Dependency Pinning Risk** | Locking a package to a specific old version (`requests==2.20.0`) prevents security updates from being applied |
| **Remediation** | Removing the version pin in `requirements.txt` ensures Lambda always installs the latest, patched version |
| **Automated Re-scan** | Every new Lambda deployment triggers Inspector to re-scan automatically — no manual action needed |
| **Finding Lifecycle** | Inspector findings move from **Active → Closed** once the vulnerability is resolved and re-scanned |

---

## My Reflection

This lab introduced a side of cloud work that is easy to overlook when focused on building: **what happens after you deploy?** The Lambda functions were running fine — but silently carrying known vulnerabilities in their dependencies. Amazon Inspector made the invisible visible.

The most practically important lesson was about **dependency pinning**. Pinning `requests==2.20.0` feels like good practice for stability — and in many cases it is — but it also means your code stays vulnerable even after a security patch is released upstream. The fix was a single line change, but understanding *why* it matters required reading the CVE entry in the National Vulnerability Database.

The automated re-scan after deployment was also a key detail: Inspector does not need to be triggered manually — it responds to new deployments on its own. In a CI/CD pipeline, that means every release is automatically checked for new vulnerabilities without any extra configuration.

> 💡 **Key Insight:** Security is not a one-time setup — it is a continuous process. Amazon Inspector automates the scanning part, but a developer still needs to understand what a CVE means, why a dependency is vulnerable, and how to fix it correctly. Automation surfaces the problem; understanding solves it.

---

*Author: Kateryna | AWS Cloud Practitioner Exam Prep*
