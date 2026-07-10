---
title : "Create Amazon ECR Repository"
date : 2026-07-10
weight : 5
chapter : false
pre : " <b> 5.2.5. </b> "
---

## Create Amazon ECR Repository

In this section, you will create an **Amazon Elastic Container Registry (Amazon ECR)** repository to store the Docker image used by the Playwright Runner container.

Amazon ECR is a fully managed container image registry that integrates seamlessly with Amazon ECS and AWS Fargate. It securely stores Docker images and provides a central location for managing container versions.

The repository created in this section will be used to:

- Store the Playwright Runner Docker image.
- Deploy containers on Amazon ECS Fargate.
- Manage Docker image versions using tags.
- Securely pull images through IAM authentication.
- Support future CI/CD automation.

---

## Step 1: Open Amazon ECR

Sign in to the **AWS Management Console**.

In the search bar, enter:

```text
Elastic Container Registry
```

Select **Amazon ECR**.

From the navigation pane, choose

```text
Repositories
```

Then choose

```text
Create repository
```

![Create Repository](/images/5-Prerequisite/5.2.5-create-ecr/create-repository.png?featherlight=false&width=90pc)

---

## Step 2: Configure the Repository

Configure the repository with the following settings.

| Property | Value |
|-----------|-------|
| Visibility settings | Private |
| Repository name | `playwright-runner` |
| Image tag mutability | `Mutable` |
| Encryption | `AES-256` |
| Scan on push | Enabled |

Leave the remaining settings as the default values.

After completing the configuration, choose

```text
Create repository
```

---

## Step 3: Verify the Repository

After a few moments, Amazon ECR creates the repository successfully.

The repository list should display the newly created repository.

![Repository Created](/images/5-Prerequisite/5.2.5-create-ecr/repository-created.png?featherlight=false&width=90pc)

Open the **playwright-runner** repository to review its configuration.

![Repository Details](/images/5-Prerequisite/5.2.5-create-ecr/repository-details.png?featherlight=false&width=90pc)

Verify the following information:

| Property | Expected Value |
|-----------|----------------|
| Repository name | `playwright-runner` |
| Repository URI | `<AWS Account ID>.dkr.ecr.ap-southeast-1.amazonaws.com/playwright-runner` |
| Visibility | `Private` |
| Tag mutability | `Mutable` |
| Encryption type | `AES-256` |
| Scan frequency | `Scan on push` |

---

## Step 4: View Push Commands

From the repository page, choose

```text
View push commands
```

Then select the **Windows** tab.

Amazon ECR automatically generates the PowerShell and Docker commands required to authenticate Docker and push container images to the repository.

![View Push Commands](/images/5-Prerequisite/5.2.5-create-ecr/view-push-commands-windows.png?featherlight=false&width=90pc)

The generated commands include:

- Authenticate Docker with Amazon ECR.
- Build the Docker image.
- Tag the Docker image.
- Push the Docker image to the repository.

> **Note**
>
> These commands are generated automatically based on your AWS account, AWS Region, and repository name.
>
> You do not need to execute these commands in this section. They will be used later in the workshop when building and pushing the Playwright Runner Docker image.

---

## Expected Result

After completing this section, an Amazon ECR repository named **playwright-runner** has been created successfully.

The repository is now ready to store Docker images that will later be deployed to Amazon ECS Fargate.

```text
Amazon ECR
└── playwright-runner
```

The repository should display the following configuration:

| Property | Value |
|-----------|-------|
| Repository name | `playwright-runner` |
| Visibility | `Private` |
| Image tag mutability | `Mutable` |
| Encryption | `AES-256` |
| Scan on push | Enabled |
