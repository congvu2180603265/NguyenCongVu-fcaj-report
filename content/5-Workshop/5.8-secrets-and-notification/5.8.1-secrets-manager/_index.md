---
title : "Secrets Manager & AI API Key"
date : 2026-06-19
weight : 1
chapter : false
pre : " <b> 5.8.1. </b> "
---

#### Store AI API Key in AWS Secrets Manager

In this section, you will create an **AWS Secrets Manager** secret to securely store the AI API key used by the `playwright-postprocessing` Lambda function.

Instead of hard-coding the API key inside the Lambda source code, the function retrieves the key dynamically from Secrets Manager during execution.

Using Secrets Manager provides the following benefits:

- Securely store sensitive credentials.
- Encrypt the API key using AWS KMS.
- Prevent accidental exposure of secrets in source code.
- Allow Lambda to retrieve the secret at runtime.
- Simplify API key management.

---

**Step 1:** Open AWS Secrets Manager

Sign in to the **AWS Management Console**.

In the search bar, search for:

```text
Secrets Manager
```

Choose **AWS Secrets Manager**, then click:

```text
Store a new secret
```

![Open AWS Secrets Manager](/images/5-Workshop/5.8-secrets-and-notification/5.8.1-secrets-manager/01-store-new-secret.png?featherlight=false&width=90pc)

---

**Step 2:** Choose the Secret Type

On the **Choose secret type** page, select:

```text
Other type of secret
```

This option is used to store custom credentials such as API Keys.

Under **Key/value pairs**, enter:

| Key | Value |
|------|------|
| api_key | Your AI API Key |

Leave the default encryption key:

```text
aws/secretsmanager
```

Then choose **Next**.

![Choose the secret type and enter the API key](/images/5-Workshop/5.8-secrets-and-notification/5.8.1-secrets-manager/02-configure-secret-value.png?featherlight=false&width=90pc)

{{% notice warning %}}
Never expose your real API Key in screenshots or public repositories. Blur or hide the value before publishing.
{{% /notice %}}

---

**Step 3:** Configure the Secret

Enter the following information:

| Property | Value |
| ------------ | ------------------------------ |
| Secret name | `playwright/openai-api-key` |
| Description | AI API Key for Playwright Lambda |

Then choose **Next**.

![Configure the secret name](/images/5-Workshop/5.8-secrets-and-notification/5.8.1-secrets-manager/03-configure-secret-name.png?featherlight=false&width=90pc)

---

**Step 4:** Configure Rotation

For this workshop, automatic rotation is not required.

Keep the default setting:

```text
Disable automatic rotation
```

Choose **Next**.

---

**Step 5:** Review and Store

Review the configuration.

Verify:

| Property | Value |
| ------ | ------ |
| Secret Type | Other type of secret |
| Secret Name | playwright/openai-api-key |
| Encryption Key | aws/secretsmanager |

Choose:

```text
Store
```

---

**Step 6:** Verify the Secret

After the secret has been created successfully, open:

```text
AWS Secrets Manager
→ Secrets
→ playwright/openai-api-key
```

The page should display:

- Secret name
- Secret ARN
- Encryption key

![Secret created successfully](/images/5-Workshop/5.8-secrets-and-notification/5.8.1-secrets-manager/04-secret-created.png?featherlight=false&width=90pc)

{{% notice note %}}
Do not click **Retrieve secret value** when taking screenshots, as it will reveal your API Key.
{{% /notice %}}

---

**Step 7:** Configure Lambda Environment Variable

Open:

```text
AWS Lambda
→ playwright-postprocessing
→ Configuration
→ Environment variables
→ Edit
```

Verify the following variable exists:

| Key | Value |
|------|-----------------------------|
| OPENAI_SECRET_NAME | playwright/openai-api-key |

The Lambda function uses this variable to determine which secret should be retrieved.

---

**Step 8:** Retrieve the Secret in Lambda

The Lambda function retrieves the secret using the following Python code.

```python
import json
import boto3
import os

secrets_client = boto3.client("secretsmanager")

def get_api_key():

    secret_name = os.environ["OPENAI_SECRET_NAME"]

    response = secrets_client.get_secret_value(
        SecretId=secret_name
    )

    secret = json.loads(response["SecretString"])

    return secret["api_key"]
```

The function automatically retrieves the latest API Key stored in Secrets Manager whenever it runs.

---

**Step 9:** Required IAM Permission

The Lambda execution role must allow the following permission:

```json
{
    "Effect":"Allow",
    "Action":[
        "secretsmanager:GetSecretValue"
    ],
    "Resource":"arn:aws:secretsmanager:*:*:secret:playwright/openai-api-key-*"
}
```

This permission allows Lambda to retrieve only the required secret.

---

#### Expected Result

After completing this section, the workflow is as follows:

```text
AWS Secrets Manager
        │
playwright/openai-api-key
        │
Encrypted by AWS KMS
        │
Lambda Environment Variable
OPENAI_SECRET_NAME
        │
playwright-postprocessing
        │
Retrieve Secret
        │
Call AI API
```

After completing this section:

- The AI API Key is securely stored in AWS Secrets Manager.
- Lambda does not need to store the API Key in the source code.
- The API Key is retrieved only when the Lambda function executes.
- Easily change or update the API Key without modifying the source code.

---

Next, we will move on to **[5.8.2. SES Email Notification](../5.8.2-notification/)** to configure the email notification channel.
