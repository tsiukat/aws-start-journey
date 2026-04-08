# Challenge Lab — AWS Lambda: Word Count Function

> **Course:** AWS Cloud Practitioner (CLF-C02)  
> **Topic:** Lambda, S3 Trigger, SNS Notification, IAM Role  
> **Date:** <!-- add date -->

---

## Lab Objective

Independent challenge — build a fully serverless word-count pipeline: a Lambda function triggered automatically when a text file is uploaded to S3, which counts the words and sends the result by email via SNS.

---

## Architecture

```
S3 Bucket (text file uploaded)
        ↓ triggers
Lambda Function (word count in Python)
        ↓ publishes
SNS Topic → Email: "The word count in <filename> is nnn."
```

---

## What I Did

### ✅ Step 1 — Created an SNS Topic
- Created a Standard SNS topic for email notifications
- Subscribed an email address and confirmed the subscription
- Copied the **Topic ARN** for use in the Lambda function

### ✅ Step 2 — Created the Lambda Function
- Runtime: **Python 3.9**
- Execution role: **LambdaAccessRole** (pre-created — grants S3, SNS, CloudWatch access)
- Wrote Python code to:
  - Read the uploaded file from the S3 event object
  - Download and decode the file content
  - Count the words using `len(content.split())`
  - Publish the result to the SNS topic with subject `Word Count Result`
  - Format message: `The word count in <filename> is nnn.`
- Added the SNS Topic ARN as an **environment variable**

### ✅ Step 3 — Created an S3 Bucket and Configured the Trigger
- Created an S3 bucket in the same region as the Lambda function
- Added an **S3 event notification**: trigger Lambda on `ObjectCreated` events for `.txt` files
- Granted S3 permission to invoke the Lambda function

### ✅ Step 4 — Tested the Function
- Uploaded multiple `.txt` files with different word counts
- Verified that each upload triggered the Lambda function automatically
- Confirmed receipt of email notification with correct word count for each file

---

## Lambda Function Code

```python
import boto3
import urllib.parse

sns_client = boto3.client('sns')
s3_client = boto3.client('s3')

TOPIC_ARN = 'arn:aws:sns:us-east-1:XXXXXXXXXXXX:WordCountTopic'  # store as env variable

def lambda_handler(event, context):
    bucket = event['Records'][0]['s3']['bucket']['name']
    key = urllib.parse.unquote_plus(event['Records'][0]['s3']['object']['key'])
    
    response = s3_client.get_object(Bucket=bucket, Key=key)
    content = response['Body'].read().decode('utf-8')
    word_count = len(content.split())
    
    message = f'The word count in the {key} file is {word_count}.'
    
    sns_client.publish(
        TopicArn=TOPIC_ARN,
        Subject='Word Count Result',
        Message=message
    )
    
    return {'statusCode': 200, 'body': message}
```

---

## Screenshots

### Lambda Function Created

<!-- Add screenshot: Lambda console showing the wordCount function overview -->
![Lambda Function](screenshots/lambda-function-overview.png)

---

### S3 Trigger Configured

<!-- Add screenshot: Lambda function Triggers tab showing S3 bucket event trigger -->
![S3 Trigger](screenshots/s3-trigger-configured.png)

---

### SNS Topic and Subscription Confirmed

<!-- Add screenshot: SNS topic with email subscription status "Confirmed" -->
![SNS Confirmed](screenshots/sns-subscription-confirmed.png)

---

### Test — File Uploaded to S3

<!-- Add screenshot: S3 bucket showing uploaded .txt test file -->
![File Uploaded](screenshots/s3-file-uploaded.png)

---

### Email Notification Received

<!-- Add screenshot: email inbox showing "Word Count Result" with correct word count -->
![Email Result](screenshots/email-word-count.png)

---

### CloudWatch Logs — Successful Execution

<!-- Add screenshot: CloudWatch log stream showing Lambda executed without errors -->
![CloudWatch Logs](screenshots/cloudwatch-logs.png)

---

## Key Takeaways

| Concept | What I Learned |
|---------|----------------|
| **Event-driven Lambda** | S3 upload automatically triggers Lambda — no polling, no manual invocation |
| **S3 Event Notification** | S3 can invoke Lambda directly on `ObjectCreated` — configurable by prefix and suffix (e.g. `.txt`) |
| **IAM Role** | Lambda needs explicit permissions for every service it touches — S3, SNS, and CloudWatch Logs |
| **Environment Variables** | Storing the SNS Topic ARN as an env variable keeps the code clean and reusable |
| **`boto3`** | The AWS SDK for Python — used to interact with S3 and SNS from inside the Lambda function |
| **CloudWatch Logs** | Every Lambda invocation is logged automatically — essential for verifying execution and debugging |

---

## My Reflection

This challenge built on the previous Lambda lab but required putting the full pipeline together independently — no guided steps, just an objective and a blank function editor.

The most important decision was the architecture: SNS topic first, then Lambda, then S3 trigger. Getting the order right mattered because the Lambda function needs the Topic ARN before the S3 trigger is attached. Working through that sequence without instructions made the dependency between services feel real.

Writing the Python code to extract the filename from the S3 event object was a new experience. The event payload is a nested JSON structure — finding the right keys (`Records[0]['s3']['bucket']['name']`) required reading the documentation, not just guessing.

Seeing the email arrive automatically after uploading a file — with the correct word count and filename — was a genuinely satisfying result. One file upload, zero manual steps, one email. That is what event-driven serverless architecture delivers in practice.

> 💡 **Key Insight:** A Lambda function is not just code — it is code plus a trigger plus permissions plus configuration. Getting all four right is what makes serverless work. Getting any one wrong means silence: no error, just no result.

---

*Author: Kateryna | AWS Cloud Practitioner Exam Prep*
