---
title : "Configure IAM"
date : 2026-06-13
weight : 3
chapter : false
pre : " <b> 5.2.3. </b> "
---

## Configure IAM roles for Lambda and ECS

In this section, you will create four IAM roles for the Playwright testing system:

| IAM role | Trusted service | Purpose |
| --- | --- | --- |
| `playwright-lambda-role` | AWS Lambda | Allows Lambda to orchestrate ECS and process results |
| `playwright-postprocessing-role` | AWS Lambda | Dedicated role for the `playwright-postprocessing` Lambda, separated to more tightly control access to Secrets Manager and SES |
| `playwright-ecs-execution-role` | ECS Tasks | Allows the ECS agent to pull images from ECR and write container logs |
| `playwright-ecs-task-role` | ECS Tasks | Grants permissions to the Playwright application inside the container |

{{% notice warning %}}
Follow the principle of least privilege. The `FullAccess` policies visible in the screenshots are suitable only for workshop demonstration. Do not grant `IAMFullAccess` to a Lambda function or ECS task in a real environment.
{{% /notice %}}

---

### 1. Create the Lambda execution role `playwright-lambda-role`

Open the **AWS Management Console**, search for **IAM**, and choose:

```text
Roles → Create role
```

**Step 1 – Select trusted entity:** Select **AWS service** as the trusted entity, choose **Lambda** under **Use case**, then click **Next**.

![Select Lambda as the trusted entity](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/1a-lambda-select-trusted-entity.png?featherlight=false&width=90pc)

**Step 2 – Add permissions:** Search for and attach the following AWS managed policies, then click **Next**.

| Policy | Purpose |
| --- | --- |
| `AmazonSQSFullAccess` | Allows Lambda to send/receive messages from SQS queues |
| `AmazonECS_FullAccess` | Allows Lambda to start and monitor ECS tasks |
| `AmazonDynamoDBFullAccess` | Allows reading/writing the workshop DynamoDB table |
| `CloudWatchLogsFullAccess` | Allows writing logs to CloudWatch Logs |
| `SecretsManagerReadWrite` | Allows reading secrets (API keys, credentials) |
| `AmazonS3FullAccess` | Allows reading/writing reports in the S3 bucket |
| `AmazonSESFullAccess` | Allows sending email through Amazon SES |
| `IAMFullAccess` | Allows Lambda to pass roles to ECS tasks |

![Attach policies for playwright-lambda-role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/1b-lambda-add-permissions.png?featherlight=false&width=90pc)

**Step 3 – Name, review, and create:** Enter the role name:

```text
playwright-lambda-role
```

![Enter name playwright-lambda-role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/1-create-lambda-role.png?featherlight=false&width=90pc)

Verify that the **Permissions policies** section shows all 8 policies above, and that the trust policy contains the `lambda.amazonaws.com` service principal, then choose **Create role**.

![Verify and create role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/2-lambda-role-created.png?featherlight=false&width=90pc)

{{% notice note %}}
For production, replace broad AWS managed `FullAccess` policies with custom policies restricted by action and resource ARN. Secrets Manager requires only `secretsmanager:GetSecretValue`. Do not grant `IAMFullAccess` — only `iam:PassRole` scoped to specific ARNs is needed.
{{% /notice %}}

---

### 2. Create a dedicated role for the Post-processing Lambda `playwright-postprocessing-role`

Besides the shared `playwright-lambda-role` from Step 1, the `playwright-postprocessing` Lambda uses **its own dedicated role** — since this function calls Secrets Manager to retrieve the AI API Key and calls SES to send email more frequently than the other Lambdas, it is separated for easier access control and to narrow permissions later.

Create another new role and select:

**Step 1 – Select trusted entity:** Select **AWS service** as the trusted entity, choose **Lambda** under **Use case**, then click **Next**.

![Select Lambda as the trusted entity](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/2a-postprocessing-select-trusted-entity.png?featherlight=false&width=90pc)

**Step 2 – Add permissions:** Search for and attach the following AWS managed policies, then click **Next**.

| Policy | Purpose |
| --- | --- |
| `AmazonDynamoDBFullAccess` | Update test result status in DynamoDB |
| `AmazonECS_FullAccess` | Allows Lambda to start and monitor ECS tasks |
| `AmazonS3FullAccess` | Read/write Playwright reports in S3 |
| `AmazonSESFullAccess` | Send test result notification emails |
| `AmazonSQSFullAccess` | Allows Lambda to send/receive messages from SQS queues |
| `AWSLambdaVPCAccessExecutionRole` | Allows Lambda to connect to VPC |
| `CloudWatchLogsFullAccess` | Write function execution logs |
| `SecretsManagerReadWrite` | Read the AI API Key from Secrets Manager |

![Attach policies for playwright-postprocessing-role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/2b-postprocessing-add-permissions.png?featherlight=false&width=90pc)

**Step 3 – Name, review, and create:** Enter the role name:

```text
playwright-postprocessing-role
```

![Enter name playwright-postprocessing-role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/2c-postprocessing-name.png?featherlight=false&width=90pc)

Verify that the **Permissions policies** section shows all 8 policies above, and that the trust policy contains the `lambda.amazonaws.com` service principal, then choose **Create role**.

![Verify and create role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/2d-postprocessing-created.png?featherlight=false&width=90pc)

{{% notice note %}}
Note down this role's ARN (in the form `arn:aws:iam::<account-id>:role/playwright-postprocessing-role`) — it will be attached to the `playwright-postprocessing` Lambda in section 5.7, instead of `playwright-lambda-role`.
{{% /notice %}}

---

### 3. Create the ECS task execution role `playwright-ecs-execution-role`

Create another new role.

**Step 1 – Select trusted entity:** Choose:

| Property | Value |
| --- | --- |
| Trusted entity type | `AWS service` |
| Service or use case | `Elastic Container Service` |
| Use case | `Elastic Container Service Task` |

Then click **Next**.

![Select ECS Tasks as the trusted service](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/3-create-ecs-execution-role.png?featherlight=false&width=90pc)

**Step 2 – Add permissions:** Search for and attach the following policy, then click **Next**.

| Policy | Purpose |
| --- | --- |
| `AmazonECSTaskExecutionRolePolicy` | Allows the ECS agent to pull images from Amazon ECR and send container logs to CloudWatch Logs |

This is the standard AWS managed policy dedicated to the ECS task execution role.

![Attach AmazonECSTaskExecutionRolePolicy](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/3b-ecs-execution-add-permissions.png?featherlight=false&width=90pc)

**Step 3 – Name, review, and create:** Enter the role name:

```text
playwright-ecs-execution-role
```

![Enter name playwright-ecs-execution-role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/5-playwright-ecs-execution-role.png?featherlight=false&width=90pc)

Verify that the trust policy uses the `ecs-tasks.amazonaws.com` service principal and that the `AmazonECSTaskExecutionRolePolicy` is attached, then choose **Create role**.

![Verify and create role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/4-ecs-execution-role-created.png?featherlight=false&width=90pc)

---

### 4. Create the ECS task role `playwright-ecs-task-role`

Create another new role.

**Step 1 – Select trusted entity:** Choose:

| Property | Value |
| --- | --- |
| Trusted entity type | `AWS service` |
| Service or use case | `Elastic Container Service` |
| Use case | `Elastic Container Service Task` |

Then click **Next**.

![Select ECS Tasks as the trusted service](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/6-ecs-task-role-created.png?featherlight=false&width=90pc)

**Step 2 – Add permissions:** The Playwright application inside the container uses this task role. Attach the following policies, then click **Next**.

| Policy | Purpose |
| --- | --- |
| `AmazonS3FullAccess` | Write reports, screenshots, and test results to the S3 bucket |
| `CloudWatchLogsFullAccess` | Write application logs to CloudWatch Logs |
| `SecretsManagerReadWrite` | Read secrets (credentials, API keys) needed at test runtime |

![Attach policies for playwright-ecs-task-role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/4b-ecs-task-add-permissions.png?featherlight=false&width=90pc)

**Step 3 – Name, review, and create:** Enter the role name:

```text
playwright-ecs-task-role
```

![Enter name playwright-ecs-task-role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/8-ecs-task-role-policies.png?featherlight=false&width=90pc)

Verify that the trust policy uses the `ecs-tasks.amazonaws.com` service principal and that all 3 policies above are attached, then choose **Create role**.

![Verify and create role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/7-create-ecs-task-role.png?featherlight=false&width=90pc)

{{% notice note %}}
For production, replace `AmazonS3FullAccess` with a custom policy that allows only `s3:PutObject` on a specific bucket ARN. Similarly, `SecretsManagerReadWrite` should be narrowed down to `secretsmanager:GetSecretValue` for the exact secret ARN.
{{% /notice %}}

---

### 5. Verify the roles

Return to **IAM → Roles** and confirm that all four roles exist:

```text
playwright-lambda-role
playwright-postprocessing-role
playwright-ecs-execution-role
playwright-ecs-task-role
```

![Created IAM roles list](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/9-lambda-role-policies.png?featherlight=false&width=90pc)

When creating the ECS task definition later, configure:

| Field | IAM role |
| --- | --- |
| Task execution role | `playwright-ecs-execution-role` |
| Task role | `playwright-ecs-task-role` |

{{% notice info %}}
**Important note on assigning IAM Roles to Lambda functions:**

- **`playwright-postprocessing` function**: Must use **`playwright-postprocessing-role`** (its dedicated role created in section 2).
- **All other Lambda functions** (`playwright-api-backend`, `playwright-coordinator`, `playwright-error-handler`): Use the shared **`playwright-lambda-role`** (created in section 1).
{{% /notice %}}
