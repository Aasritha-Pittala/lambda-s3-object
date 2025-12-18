# Project 3 – AWS Lambda Triggered by S3 Object Uploads

## Overview
In this project, I created an AWS Lambda function that automatically reacts whenever a new file is uploaded to an S3 bucket. This simulates real-world automation scenarios like logging file metadata, virus scanning, or generating thumbnails.

By completing this project, I learned how to connect S3 events with Lambda functions, configure IAM roles securely, and monitor outputs using CloudWatch Logs.

---

## Services and Tools Used
- Amazon S3
- AWS Lambda
- IAM Roles & Policies
- CloudWatch Logs
- Python (Lambda runtime)

---

## What I Did

### 1. Created an S3 Bucket
I created a new S3 bucket for testing purposes.  
- Enabled versioning to keep track of multiple versions of uploaded files.  
- This bucket serves as the source of events to trigger the Lambda function.

---

### 2. Created IAM Role for Lambda
I created an IAM role for the Lambda function with the following permissions:  
- **AmazonS3ReadOnlyAccess** – so Lambda can read uploaded object details  
- **CloudWatchLogsFullAccess** – to log outputs for monitoring  

This ensured that the Lambda function could securely access S3 and log outputs.

---

### 3. Created the Lambda Function
I created a Lambda function in Python that captures object details whenever a file is uploaded to S3. Here’s the code I used:

```python
import json
import boto3

def lambda_handler(event, context):
    s3 = boto3.client('s3')
    bucket = event['Records'][0]['s3']['bucket']['name']
    key = event['Records'][0]['s3']['object']['key']
    response = s3.head_object(Bucket=bucket, Key=key)

    details = {
        "FileName": key,
        "FileSize": response['ContentLength'],
        "FileType": response['ContentType'],
        "LastModified": response['LastModified'].strftime("%Y-%m-%d %H:%M:%S"),
        "Metadata": response.get('Metadata', {})
    }

    print("Uploaded File Details:", json.dumps(details))
    return details
```
I deployed the function and attached the IAM role I created earlier.

4. Configured S3 Event Trigger
I added an S3 Event Trigger to the bucket:

Event type: ObjectCreated

Target: Lambda function

This ensures that every time a new file is uploaded, the Lambda is invoked automatically.

5. Tested the Setup
Uploaded a sample file (PDF) to the bucket

Checked CloudWatch Logs for Lambda output

Confirmed that the Lambda correctly logged the file name, size, type, and metadata
