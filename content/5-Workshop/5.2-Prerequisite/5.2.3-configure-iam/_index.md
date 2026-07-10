---
title : "Configure IAM"
date : 2026-07-10
weight : 3
chapter : false
pre : " <b> 5.2.3. </b> "
---

#### Configure IAM roles for Lambda and ECS

In this section, you will create three IAM roles for the Playwright testing system:

| IAM role | Trusted service | Purpose |
|---|---|---|
| `playwright-lambda-role` | AWS Lambda | Allows Lambda to orchestrate ECS and process results |
| `playwright-ecs-execution-role` | ECS Tasks | Allows ECS to pull images from ECR and write logs |
| `playwright-ecs-task-role` | ECS Tasks | Grants permissions to the application inside the container |

{{% notice warning %}}
Follow the principle of least privilege. The `FullAccess` policies visible in the screenshots are suitable only for workshop demonstration. Do not grant `IAMFullAccess` to a Lambda function or ECS task in a real environment.
{{% /notice %}}

---

### Step 1: Create the Lambda execution role

Open the **AWS Management Console**, search for **IAM**, and choose:

```text
Roles
→ Create role
```

Select **AWS service** as the trusted entity and select the **Lambda** use case.

For the role name, enter:

```text
playwright-lambda-role
```

![Create the Lambda role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/create-lambda-role.png?featherlight=false&width=90pc)

The post-processing Lambda requires these permission groups:

- Write logs to CloudWatch Logs.
- Read ECS task status.
- Read ECS task logs.
- Read and write reports in `playwright-report-2026`.
- Update the workshop DynamoDB table.
- Read the `playwright/openai-api-key` secret.
- Send email through Amazon SES.

![Policies for the Lambda role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/lambda-role-created.png?featherlight=false&width=90pc)

{{% notice note %}}
For production, replace broad AWS managed `FullAccess` policies with custom policies restricted by action and resource ARN. Secrets Manager requires only `secretsmanager:GetSecretValue`; `SecretsManagerReadWrite` is unnecessary.
{{% /notice %}}

Verify that the trust policy contains the `lambda.amazonaws.com` service principal, and then choose **Create role**.

---

### Step 2: Create the ECS task execution role

Create another role and select:

| Property | Value |
|---|---|
| Trusted entity type | `AWS service` |
| Service or use case | `Elastic Container Service` |
| Use case | `Elastic Container Service Task` |

![Select ECS Tasks as the trusted service](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/create-ecs-execution-role.png?featherlight=false&width=90pc)

Attach this AWS managed policy:

```text
AmazonECSTaskExecutionRolePolicy
```

This policy allows the ECS agent to pull images from Amazon ECR and send container logs to CloudWatch Logs.

![Policy for the ECS execution role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/ecs-execution-role-created.png?featherlight=false&width=90pc)

Enter the role name:

```text
playwright-ecs-execution-role
```

![Name the ECS execution role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/playwright-ecs-execution-role.png?featherlight=false&width=90pc)

Verify that the trust policy uses the `ecs-tasks.amazonaws.com` service principal, and then choose **Create role**.

---

### Step 3: Create the ECS task role

Create another role with the same trusted service:

```text
Elastic Container Service Task
```

![Create the ECS task role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/ecs-task-role-created.png?featherlight=false&width=90pc)

The Playwright application inside the container uses the task role. Grant only the permissions that the application actually requires, such as:

- Read test scripts from the report bucket.
- Write reports, screenshots, and results to the required bucket prefixes.
- Write application logs if the application calls CloudWatch Logs directly.

The workshop screenshot demonstrates selecting S3 and CloudWatch Logs policies:

![Policies for the ECS task role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/create-ecs-task-role.png?featherlight=false&width=90pc)

Enter the role name:

```text
playwright-ecs-task-role
```

![Name the ECS task role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/ecs-task-role-policies.png?featherlight=false&width=90pc)

Then choose **Create role**.

---

### Step 4: Verify the roles

Return to **IAM → Roles** and confirm that all three roles exist:

```text
playwright-lambda-role
playwright-ecs-execution-role
playwright-ecs-task-role
```

![Created IAM roles](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/lambda-role-policies.png?featherlight=false&width=90pc)

When creating the ECS task definition later, configure:

| Field | IAM role |
|---|---|
| Task execution role | `playwright-ecs-execution-role` |
| Task role | `playwright-ecs-task-role` |

The `playwright-postprocessing` Lambda function uses `playwright-lambda-role` as its execution role.
