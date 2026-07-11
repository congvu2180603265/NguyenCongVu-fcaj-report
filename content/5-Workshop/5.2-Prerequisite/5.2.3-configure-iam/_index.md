---
title : "Configure IAM"
date : 2026-06-13
weight : 3
chapter : false
pre : " <b> 5.2.3. </b> "
---

#### Configure IAM roles for Lambda and ECS

In this section, you will create four IAM roles for the Playwright testing system:

| IAM role | Trusted service | Purpose |
|---|---|---|
| `playwright-lambda-role` | AWS Lambda | Allows Lambda to orchestrate ECS and process results |
| `playwright-postprocessing-role` | AWS Lambda | Dedicated role for the `playwright-postprocessing` Lambda, separated to more tightly control access to Secrets Manager and SES |
| `playwright-ecs-execution-role` | ECS Tasks | Allows ECS to pull images from ECR and write logs |
| `playwright-ecs-task-role` | ECS Tasks | Grants permissions to the application inside the container |

{{% notice warning %}}
Follow the principle of least privilege. The `FullAccess` policies visible in the screenshots are suitable only for workshop demonstration. Do not grant `IAMFullAccess` to a Lambda function or ECS task in a real environment.
{{% /notice %}}

---

**Step 1:** Create the Lambda execution role

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

![Create the Lambda role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/1-create-lambda-role.png?featherlight=false&width=90pc)

The post-processing Lambda requires these permission groups:

- Write logs to CloudWatch Logs.
- Read ECS task status.
- Read ECS task logs.
- Read and write reports in `playwright-report-2026`.
- Update the workshop DynamoDB table.
- Read the `playwright/openai-api-key` secret.
- Send email through Amazon SES.

![Policies for the Lambda role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/2-lambda-role-created.png?featherlight=false&width=90pc)

{{% notice note %}}
For production, replace broad AWS managed `FullAccess` policies with custom policies restricted by action and resource ARN. Secrets Manager requires only `secretsmanager:GetSecretValue`; `SecretsManagerReadWrite` is unnecessary.
{{% /notice %}}

Verify that the trust policy contains the `lambda.amazonaws.com` service principal, and then choose **Create role**.

---

**Step 2:** Create a dedicated role for the Post-processing Lambda

Besides the shared `playwright-lambda-role` from Step 1, the `playwright-postprocessing` Lambda uses **its own dedicated role** — since this function calls Secrets Manager to retrieve the AI API Key and calls SES to send email more than the other Lambdas in the system, it's separated out for easier control and to narrow down permissions later.

Create another new role, select:

| Property | Value |
|---|---|
| Trusted entity type | `AWS service` |
| Service or use case | `Lambda` |

Attach the same permission groups as Step 1 (CloudWatch Logs, DynamoDB, Secrets Manager, S3, SES).

Enter the role name:

```text
playwright-postprocessing-role
```

![Role playwright-postprocessing-role created](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/2b-postprocessing-role-created.jpeg?featherlight=false&width=90pc)

{{% notice note %}}
Note down this role's ARN (in the form `arn:aws:iam::<account-id>:role/playwright-postprocessing-role`) — it will be attached to the `playwright-postprocessing` Lambda in section 5.7, instead of `playwright-lambda-role`.
{{% /notice %}}

Verify that the trust policy contains the `lambda.amazonaws.com` service principal, and then choose **Create role**.

---

**Step 3:** Create the ECS task execution role

Create another role and select:

| Property | Value |
|---|---|
| Trusted entity type | `AWS service` |
| Service or use case | `Elastic Container Service` |
| Use case | `Elastic Container Service Task` |

![Select ECS Tasks as the trusted service](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/3-create-ecs-execution-role.png?featherlight=false&width=90pc)

Attach this AWS managed policy:

```text
AmazonECSTaskExecutionRolePolicy
```

This policy allows the ECS agent to pull images from Amazon ECR and send container logs to CloudWatch Logs.

![Policy for the ECS execution role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/4-ecs-execution-role-created.png?featherlight=false&width=90pc)

Enter the role name:

```text
playwright-ecs-execution-role
```

![Name the ECS execution role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/5-playwright-ecs-execution-role.png?featherlight=false&width=90pc)

Verify that the trust policy uses the `ecs-tasks.amazonaws.com` service principal, and then choose **Create role**.

---

**Step 4:** Create the ECS task role

Create another role with the same trusted service:

```text
Elastic Container Service Task
```

![Create the ECS task role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/6-ecs-task-role-created.png?featherlight=false&width=90pc)

The Playwright application inside the container uses the task role. Grant only the permissions that the application actually requires, such as:

- Read test scripts from the report bucket.
- Write reports, screenshots, and results to the required bucket prefixes.
- Write application logs if the application calls CloudWatch Logs directly.

The workshop screenshot demonstrates selecting S3 and CloudWatch Logs policies:

![Policies for the ECS task role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/7-create-ecs-task-role.png?featherlight=false&width=90pc)

Enter the role name:

```text
playwright-ecs-task-role
```

![Name the ECS task role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/8-ecs-task-role-policies.png?featherlight=false&width=90pc)

Then choose **Create role**.

---

**Step 5:** Verify the roles

Return to **IAM → Roles** and confirm that all four roles exist:

```text
playwright-lambda-role
playwright-postprocessing-role
playwright-ecs-execution-role
playwright-ecs-task-role
```

![Created IAM roles](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/9-lambda-role-policies.png?featherlight=false&width=90pc)

When creating the ECS task definition later, configure:

| Field | IAM role |
|---|---|
| Task execution role | `playwright-ecs-execution-role` |
| Task role | `playwright-ecs-task-role` |

The `playwright-postprocessing` Lambda function uses **`playwright-postprocessing-role`** as its execution role (not the shared `playwright-lambda-role`). The remaining Lambda functions (`playwright-api-backend`, `playwright-coordinator`, `playwright-error-handler`) continue to use `playwright-lambda-role`.
