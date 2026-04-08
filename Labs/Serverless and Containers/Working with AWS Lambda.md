# 178-[JAWS]-Activity - Working with AWS Lambda

> **Course:** AWS Cloud Practitioner (CLF-C02)  
> **Topic:** Serverless Computing, Lambda Functions, SNS, CloudWatch Events  
> **Date:** <!-- add date -->

---

## Lab Objective

Deploy a serverless sales analysis reporting solution using AWS Lambda. The system automatically queries a café database and emails a daily report — without any servers to manage.

---

## Architecture

```
CloudWatch Events (schedule)
        ↓
salesAnalysisReport (Lambda)
        ↓ invokes
salesAnalysisReportDataExtractor (Lambda)
        ↓ queries
Café Database (EC2 LAMP)
        ↓ results returned
salesAnalysisReport (Lambda)
        ↓ publishes
SNS Topic → Email to administrator
```

---

## Tasks Completed

### ✅ Task 1 — Reviewed IAM Roles
- `salesAnalysisReportRole` — grants SNS, SSM, CloudWatch Logs, and Lambda invoke permissions
- `salesAnalysisReportDERole` — grants CloudWatch Logs and VPC access for database connectivity
- Key principle: each Lambda function gets only the permissions it needs

### ✅ Task 2 — Created Lambda Layer and Data Extractor Function
- Created a **Lambda Layer** `pymysqlLibrary` from `pymysql-v3.zip` (Python 3.9) — reusable MySQL client library
- Created Lambda function `salesAnalysisReportDataExtractor` using `salesAnalysisReportDERole`
- Attached the layer to the function
- Uploaded function code from `salesAnalysisReportDataExtractor-v3.zip`
- Configured **VPC settings**: Cafe VPC, Cafe Public Subnet 1, CafeSecurityGroup

### ✅ Task 3 — Tested and Troubleshot the Data Extractor
- Created test event with database connection parameters from **SSM Parameter Store**
- First test failed: **timeout after 3 seconds**
- **Root cause:** Security group had no inbound rule for port 3306 (MySQL) — Lambda could not connect to the database
- **Fix:** Added inbound rule for TCP port 3306 to CafeSecurityGroup
- Placed orders on café website to populate database → re-tested → function returned order data successfully

### ✅ Task 4 — Configured SNS Notifications
- Created SNS topic `salesAnalysisReportTopic`
- Subscribed an email address → confirmed subscription

### ✅ Task 5 — Created and Configured the Main Lambda Function
- Created `salesAnalysisReport` function via **AWS CLI** using `aws lambda create-function`
- Set environment variable `topicARN` with the SNS topic ARN
- Tested the function → received "Daily Sales Analysis Report" email successfully
- Added **CloudWatch Events trigger** with a cron expression to run Monday–Saturday at 8 PM UTC

---

## Screenshots

### Lambda Layer Created
<img width="1920" height="1040" alt="task2-lambda-layer" src="https://github.com/user-attachments/assets/a0ed17b0-c490-4721-af09-fba17b1cec42" />

---

### Task 3 — Timeout Error (Before Fix)
<img width="1920" height="1040" alt="Test Failed" src="https://github.com/user-attachments/assets/6cf4fe75-3bc7-4486-8685-06e496dd68c9" />

---

### Task 3 — Security Group Fix (Port 3306 Added)
<img width="1920" height="1040" alt="Test succeeded" src="https://github.com/user-attachments/assets/df82389b-0869-40cd-ad4e-abe37f7443cf" />

---

### Task 3 — Data Extractor Test Succeeded
<img width="1920" height="1040" alt="task3-test-success" src="https://github.com/user-attachments/assets/8c40af1e-96a1-447f-8279-c4aed6425a66" />

---

### Task 5 — Sales Report Email Received
<img width="1920" height="1040" alt="Sales Report Email Received - confirmation" src="https://github.com/user-attachments/assets/b857b94c-0cc5-4b37-8168-cb5aaf0d84d0" />
<img width="1920" height="1040" alt="Sales Report Email Received - email" src="https://github.com/user-attachments/assets/3c6892a3-ce41-4ee4-b2ad-071a8735ed34" />

---

### Task 5 — CloudWatch Events Trigger Configured
<img width="1920" height="1040" alt="Trigger" src="https://github.com/user-attachments/assets/4ce4fcc1-5ec5-40cf-9c05-1f843c20a3f8" />
Triggered on Time
<img width="1920" height="1040" alt="Triggered on time" src="https://github.com/user-attachments/assets/263067c6-6c60-47e9-8e91-513bb6a700df" />


---

## Key Takeaways

| Concept | What I Learned |
|---------|----------------|
| **Serverless** | Lambda runs code without provisioning or managing servers — you pay only for execution time |
| **Lambda Layer** | A reusable package of code or libraries shared across multiple functions — avoids duplicating dependencies |
| **IAM + Lambda** | Every Lambda function needs an execution role — permissions are not inherited automatically |
| **VPC Access** | Lambda functions that access resources inside a VPC need subnet and security group configuration |
| **Security Group as Root Cause** | A missing inbound rule on port 3306 silently caused a timeout — no obvious error, just a connection that never completed |
| **SSM Parameter Store** | A secure way to store and retrieve configuration values (DB credentials) without hardcoding them in function code |
| **CloudWatch Events / EventBridge** | Cron-based scheduling to trigger Lambda automatically — no external scheduler needed |
| **CloudWatch Logs** | Every Lambda execution writes logs — essential for diagnosing failures |

---

## My Reflection

This lab was the most complex so far — not because of individual steps, but because it required multiple AWS services working together correctly at the same time. Lambda, IAM, VPC, Security Groups, SSM, SNS, and CloudWatch Events all had to be configured precisely for the final email to arrive.

The troubleshooting moment in Task 3 was the most instructive part. The function timed out with no obvious error message — just a 3-second countdown and silence. Tracing the problem back to a missing security group rule on port 3306 was a real diagnostic exercise: understand what the function does, identify what it needs to connect to, check whether the network actually allows that connection.

The **Lambda Layer** concept also stood out. Instead of bundling the PyMySQL library into every function that needs it, one layer serves all. That is a clean separation of concerns — and it maps directly to how production Lambda environments are maintained at scale.

> 💡 **Key Insight:** Serverless does not mean "no configuration." It means no servers — but permissions, networking, environment variables, and triggers all still need to be designed carefully. The architecture is simpler, but the details still matter.

---

*Author: Kateryna | AWS Cloud Practitioner Exam Prep*

