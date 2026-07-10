---
title : "Lambda Post-processing & AI"
date : 2026-07-10
weight : 4
chapter : false
pre : " <b> 5.7.4. </b> "
---

#### Deploy Lambda Post-processing & AI

In this section, you will deploy the **playwright-postprocessing** Lambda function. This component is responsible for processing the result after Amazon ECS Fargate finishes running automated Playwright tests.

Lambda Post-processing performs the following main tasks:

- Read test logs from Amazon CloudWatch Logs.
- Collect the ECS task status and result.
- Call the AI API to analyze and summarize errors.
- Generate the test report in HTML or JSON format.
- Upload the report to Amazon S3.
- Create a presigned URL for accessing the report.
- Update the result in Amazon DynamoDB.
- Send notification email through Amazon SES.

---

### Step 1: Open AWS Lambda

Sign in to the **AWS Management Console**.

In the search bar, enter:

```text
Lambda
```

Select **AWS Lambda**, then choose **Create function**.

![Create Lambda Function](/images/5-Workshop/5.7-lambda-functions/5.7.4-post-processing/create-lambda.png?featherlight=false&width=90pc)

---

### Step 2: Define Lambda Information

In the **Create function** page, select:

```text
Author from scratch
```

Enter the following information:

| Property | Value |
|---|---|
| Function name | `playwright-postprocessing` |
| Runtime | `Python 3.12` |
| Architecture | `x86_64` |

![Configure Lambda Information](/images/5-Workshop/5.7-lambda-functions/5.7.4-post-processing/lambda-config.png?featherlight=false&width=90pc)

In **Permissions** or **Additional settings**, choose the IAM role created earlier:

```text
playwright-lambda-role
```

This IAM role needs permissions to access:

- Amazon CloudWatch Logs
- Amazon S3
- Amazon DynamoDB
- AWS Secrets Manager
- Amazon SES
- Amazon ECS

After checking all information, choose **Create function**.

---

### Step 3: Update Lambda Source Code

After the Lambda function is created successfully, open the **Code** tab.

In **Code source**, update the file:

```text
lambda_function.py
```

If the source code includes report templates, the structure can include:

```text
lambda_function.py
templates/
|-- email_report.html
`-- style.css
```

After finishing the update, choose **Deploy** to save the source code.

![Update Lambda Source Code](/images/5-Workshop/5.7-lambda-functions/5.7.4-post-processing/upload-code.png?featherlight=false&width=90pc)

---

### Step 4: Configure Environment Variables

Open:

```text
Configuration
-> Environment variables
-> Edit
```

Add the following environment variables:

| Key | Value |
|---|---|
| `LOG_GROUP_NAME` | `/ecs/playwright-runner` |
| `TEST_HISTORY_TABLE` | `playwright-test-history` |
| `REPORT_BUCKET` | `playwright-report-2026` |
| `OPENAI_SECRET_NAME` | `playwright/openai-api-key` |
| `SES_SENDER_EMAIL` | Email address verified in Amazon SES |

![Configure Environment Variables](/images/5-Workshop/5.7-lambda-functions/5.7.4-post-processing/eve-var.png?featherlight=false&width=90pc)

Meaning of each variable:

- `LOG_GROUP_NAME`: the CloudWatch Log Group that stores ECS Fargate logs.
- `TEST_HISTORY_TABLE`: the DynamoDB table used to store test history.
- `REPORT_BUCKET`: the S3 bucket used to store reports.
- `OPENAI_SECRET_NAME`: the secret name that stores the AI API key in Secrets Manager.
- `SES_SENDER_EMAIL`: the sender email address verified in Amazon SES.

After entering all variables, choose **Save**.

---

### Step 5: Configure VPC

Open:

```text
Configuration
-> VPC
-> Edit
```

Configure the following values:

| Property | Value |
|---|---|
| VPC | `playwright-vpc` |
| Subnet | `playwright-private-subnet` |
| Security Group | `playwright-sg-lambda` |

![Configure VPC for Lambda](/images/5-Workshop/5.7-lambda-functions/5.7.4-post-processing/vpc-config.png?featherlight=false&width=90pc)

The Lambda function is placed in the private subnet so it can access internal resources through a VPC endpoint or NAT gateway.

Choose **Save** to save the configuration.

---

### Step 6: Configure Timeout

Open:

```text
Configuration
-> General configuration
-> Edit
```

Set:

```text
Timeout = 30 seconds
```

![Configure Timeout](/images/5-Workshop/5.7-lambda-functions/5.7.4-post-processing/timeout-configuration.png?featherlight=false&width=90pc)

Lambda Post-processing needs more time than a simple Lambda function because it must:

- Read CloudWatch Logs.
- Call the AI API.
- Generate the report.
- Upload the file to S3.
- Update DynamoDB.
- Send email through SES.

Then choose **Save**.

---

### Step 7: Create an EventBridge Rule

Lambda Post-processing is not triggered by Amazon SQS. This Lambda function is invoked when the ECS task finishes and changes to the `STOPPED` state.

Open:

```text
Amazon EventBridge
-> Rules
-> Create rule
```

Configure:

| Property | Value |
|---|---|
| Rule name | `playwright-postprocessing-rule` |
| Rule type | `Rule with an event pattern` |
| Event source | `AWS events` |
| AWS service | `Elastic Container Service` |
| Event type | `ECS Task State Change` |

![Create EventBridge Rule](/images/5-Workshop/5.7-lambda-functions/5.7.4-post-processing/create-eventbridge-rule.png?featherlight=false&width=90pc)

---

### Step 8: Define the Event Pattern

In **Event pattern**, enter the following content:

```json
{
  "source": ["aws.ecs"],
  "detail-type": ["ECS Task State Change"],
  "detail": {
    "lastStatus": ["STOPPED"],
    "clusterArn": [
      "arn:aws:ecs:ap-southeast-1:238337501662:cluster/playwright-cluster"
    ]
  }
}
```

Filtering by `clusterArn` helps prevent the Lambda function from being invoked by unrelated ECS tasks in the same AWS account.

---

### Step 9: Select the EventBridge Target

In **Target**, choose:

| Property | Value |
|---|---|
| Target type | `AWS service` |
| Select a target | `Lambda function` |
| Function | `playwright-postprocessing` |

Finish creating the rule.

Return to Lambda:

```text
Lambda
-> playwright-postprocessing
-> Configuration
-> Triggers
```

Check that the EventBridge trigger is displayed.

---

### Step 10: Review the Completed Lambda Function

After finishing the configuration, the Lambda function should meet the following requirements:

- Function name is `playwright-postprocessing`.
- Runtime is Python 3.12.
- IAM role is `playwright-lambda-role`.
- Lambda is placed in `playwright-vpc`.
- Lambda uses `playwright-private-subnet`.
- Security Group is `playwright-sg-lambda`.
- Timeout is 30 seconds.
- All five environment variables are configured.
- EventBridge trigger is configured for the ECS task `STOPPED` state.

---

### Expected Result

When ECS Fargate completes the test run, the post-processing flow works as follows:

```text
ECS Fargate Task
        |
Task changes to STOPPED
        |
Amazon EventBridge
        |
Lambda playwright-postprocessing
        |
Read CloudWatch Logs
        |
Call AI API to analyze errors
        |
Generate HTML/JSON report
        |
Upload report to Amazon S3
        |
Update DynamoDB
        |
Send email through Amazon SES
```

After the Lambda function runs successfully, the `playwright-test-history` table is updated with the following fields:

```text
status
finished_at
report_url
ai_summary
```

The report is stored in the following bucket:

```text
playwright-report-2026
```

Users can access the report through the presigned URL generated by Lambda.
