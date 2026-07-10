---
title : "Lambda Functions"
date : 2024-01-01
weight : 7
chapter : false
pre : " <b> 5.7. </b> "
---

### Overview

In this section, you will deploy four AWS Lambda functions that form the core orchestration layer of the serverless Playwright testing system. These functions handle API requests, coordinate ECS tasks, manage errors, and process post-test reports.

You will configure the following functions:
1. **playwright-api-backend**: Serves as the backend for the API Gateway to handle user requests.
2. **playwright-coordinator**: Triggered by an SQS queue to launch ECS Fargate tasks.
3. **playwright-error-handler**: Triggered by a Dead Letter Queue (DLQ) to handle failed messages.
4. **playwright-postprocessing**: Triggered by EventBridge to process test results after an ECS task completes.

---

### 1. Create `playwright-api-backend`

This function acts as the integration point for your API Gateway.

1. Navigate to the **AWS Lambda Console**.
2. Click **Create function**.
3. Select **Author from scratch**.
4. Set the **Function name** to `playwright-api-backend`.
5. Select `Python 3.14` (or the latest available Python version) as the **Runtime**.
![Create API Backend](/images/5-Workshop/5.7-lambda-functions/create-function-api-backend.png)
6. Under **Permissions**, expand **Change default execution role**.
7. Select **Use an existing role** and choose the `playwright-lambda-role` that was created in the prerequisite phase.
![Execution Role](/images/5-Workshop/5.7-lambda-functions/create-function-role.png)
8. Click **Create function**.
9. Scroll down to the **Code source** section, click **Upload from** and select **.zip file**. Choose the `playwright-api-backend.zip` file you prepared in the Prerequisites section and click **Save**.
![Upload ZIP](/images/5-Workshop/5.7-lambda-functions/upload-zip.png)
10. Navigate to the **Configuration** tab, then select **Environment variables**. Click **Edit**.
11. Add the following key-value pairs based on the resources created in previous steps:
    - `COGNITO_USER_POOL_ID`: Your Cognito User Pool ID (e.g., `ap-southeast-1_...`)
    - `EMAIL_CONFIG_TABLE`: `playwright-email-config`
    - `REPORT_BUCKET`: `playwright-report-2026` (or your unique bucket name)
    - `SCHEDULER_ROLE_ARN`: The ARN of your `playwright-lambda-role`
    - `TASK_QUEUE_URL`: The URL of your `playwright-task-queue`
    - `TEST_HISTORY_TABLE`: `playwright-test-history`
    - `TEST_SUITES_TABLE`: `playwright-test-suites`
![API Backend Environment Variables](/images/5-Workshop/5.7-lambda-functions/env-vars-api-backend.png)

---

### 2. Create `playwright-coordinator`

This function listens to the task queue and launches the ECS Fargate tasks.

1. Create a new function named `playwright-coordinator` using the same Python runtime and the `playwright-lambda-role`.
2. Scroll down to the **Code source** section, click **Upload from** and select **.zip file**. Choose the `playwright-coordinator.zip` file you prepared in the Prerequisites section and click **Save**.
3. In the function overview, click **Add trigger**.
3. Select **SQS** as the source.
4. Choose the `playwright-task-queue` you created earlier.
5. Set the **Batch size** to `1`. This ensures that each ECS task is launched individually per request. Click **Add**.
![Coordinator SQS Trigger](/images/5-Workshop/5.7-lambda-functions/sqs-trigger-coordinator.png)
6. Go to the **Configuration** tab, select **General configuration**, and click **Edit**.
7. Adjust the **Timeout** to `30` seconds and the **Ephemeral storage** to `512` MB. Click **Save**.
![Coordinator Timeout](/images/5-Workshop/5.7-lambda-functions/timeout-coordinator.png)
8. Navigate to **Environment variables** and add the following keys with their corresponding values (VPC Subnets, Security Groups, Cluster names, etc.):
    - `ASSIGN_PUBLIC_IP`: `DISABLED`
    - `CONTAINER_NAME`: `playwright-container`
    - `ECS_CLUSTER_NAME`: `playwright-cluster`
    - `ECS_TASK_DEFINITION`: `playwright-task-def`
    - `SECURITY_GROUP_IDS`: Your ECS Security Group ID (e.g., `sg-...`)
    - `SUBNET_IDS`: Your Private Subnet ID (e.g., `subnet-...`)
    - `TEST_HISTORY_TABLE`: `playwright-test-history`
![Coordinator Environment Variables](/images/5-Workshop/5.7-lambda-functions/env-vars-coordinator.png)

---

### 3. Create `playwright-error-handler`

This function processes messages that fail to be processed by the coordinator.

1. Create a new function named `playwright-error-handler` using the same Python runtime and the `playwright-lambda-role`.
2. Scroll down to the **Code source** section, click **Upload from** and select **.zip file**. Choose the `playwright-error-handler.zip` file you prepared in the Prerequisites section and click **Save**.
3. Click **Add trigger**.
3. Select **SQS** and choose the `playwright-dlq` (Dead Letter Queue).
4. Set the **Batch size** to `3`. Click **Add**.
![Error Handler SQS Trigger](/images/5-Workshop/5.7-lambda-functions/sqs-trigger-error-handler.png)
5. Navigate to **Environment variables** and add the following:
    - `ERROR_DYNAMODB_TABLE`: `playwright-error-log`
    - `TEST_HISTORY_TABLE`: `playwright-test-history`
![Error Handler Environment Variables](/images/5-Workshop/5.7-lambda-functions/env-vars-error-handler.png)

---

### 4. Create `playwright-postprocessing`

This function is triggered by EventBridge after an ECS task finishes. It processes execution logs, calls the OpenAI API for analysis, and sends email notifications via SES.

1. Create a new function named `playwright-postprocessing`.
2. Select `Python 3.14` as the **Runtime**.
3. Under **Additional settings**, enable **Custom execution role** and select `playwright-postprocessing-role`.
4. Scroll down to the **Code source** section, click **Upload from** and select **.zip file**. Upload the `playwright-postprocessing.zip` file you prepared in the Prerequisites section and click **Save**.
5. Go to the **Configuration** tab, select **General configuration**, and click **Edit**. Set the **Timeout** to `2` min `0` sec. Click **Save**.
6. Select **VPC** and click **Edit**. Configure as follows, then click **Save**.
   - **VPC**: Select your `playwright-vpc`
   - **Subnets**: Select `playwright-private-subnet`
   - **Security groups**: Select `playwright-sg-lambda`
![Postprocessing VPC Configuration](/images/5-Workshop/5.7-lambda-functions/postprocessing-vpc.png)
7. Select **Environment variables** and click **Edit**. Add the following:
   - `EMAIL_CONFIG_TABLE`: `playwright-email-config`
   - `LOG_GROUP_NAME`: `/ecs/playwright-runner`
   - `OPENAI_SECRET_NAME`: `playwright/openai-api-key`
   - `REPORT_BUCKET`: `playwright-report-2026`
   - `SES_SENDER_EMAIL`: Your verified SES sender email address
   - `TEST_HISTORY_TABLE`: `playwright-test-history`
![Postprocessing Environment Variables](/images/5-Workshop/5.7-lambda-functions/postprocessing-env-vars.png)

> [!NOTE]
> **Important Note regarding EventBridge Trigger**
> The trigger for `playwright-postprocessing` will be configured from the EventBridge console in a later section. When EventBridge invokes a Lambda function via an IAM Role (instead of a resource-based policy), the AWS Lambda Console will **not** display an EventBridge entry in the "Triggers" tab. This is a known UI limitation of the AWS Console and does not affect functionality.

---

All four Lambda functions have been successfully deployed and configured. Next, we will move on to **[5.8. Secrets & Notification](../5.8-secrets-and-notification/)** to set up AWS Secrets Manager to store the OpenAI API key and configure Amazon SES to send test result email notifications.
