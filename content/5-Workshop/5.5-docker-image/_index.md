---
title : "Docker Image"
date : 2026-07-10
weight : 5
chapter : false
pre : " <b> 5.5. </b> "
---

#### Build and Push Playwright Docker Image to Amazon ECR

In this section, you will build a Docker image containing the Playwright testing script and push it to the Amazon ECR repository (`playwright-runner`) created in the previous section.

### 1. Prepare the Dockerfile

First, ensure that you have cloned the test source code from GitHub (the link was provided in **[5.2.5. Create ECR](../5.2-prerequisite/5.2.5-create-ecr/)**). The `Dockerfile` to package Playwright is already included in that source code directory.

*(You do not need to create this file yourself, below is the content of that file for your reference)*:

```dockerfile
# Use the official Playwright image
FROM mcr.microsoft.com/playwright:v1.44.0-jammy

# Set the working directory
WORKDIR /app

# Copy package configuration files (package.json, package-lock.json)
COPY package*.json ./

# Install dependencies
RUN npm ci

# Copy the entire test source code into the container
COPY . .

# Default command to run when the container starts (will run tests)
ENTRYPOINT ["node", "entrypoint.js"]
```

### 2. Steps to Push the Image to ECR

To execute the commands below, you can copy them directly or view them in the AWS ECR Console by selecting the `playwright-runner` repository and clicking **"View push commands"**.

**Step 1: Authenticate the Docker client with Amazon ECR**

Retrieve an authentication token and pass it to the Docker login command:

```bash
aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin 238337501662.dkr.ecr.ap-southeast-1.amazonaws.com
```

![ECR authentication successful](/images/5-Workshop/5.5-docker-image/1-docker-login.png?featherlight=false&width=90pc)

**Step 2: Build the Docker image**

Run the following command in the directory containing the `Dockerfile` and Playwright source code:

```bash
docker build -t playwright-runner .
```

**Step 3: Tag the Docker image**

After the build completes, tag the image so it can be pushed to your repository:

```bash
docker tag playwright-runner:latest 238337501662.dkr.ecr.ap-southeast-1.amazonaws.com/playwright-runner:latest
```

**Step 4: Push the Docker image to AWS ECR**

Finally, push the image to the ECR repository:

```bash
docker push 238337501662.dkr.ecr.ap-southeast-1.amazonaws.com/playwright-runner:latest
```

![Image successfully pushed to ECR](/images/5-Workshop/5.5-docker-image/2-docker-push.png?featherlight=false&width=90pc)

{{% notice tip %}}
**Tip:** Ensure you have installed and configured the `aws-cli` on your machine with full ECR access permissions (`AdministratorAccess` or `AmazonEC2ContainerRegistryFullAccess`) before running the commands above.
{{% /notice %}}

---

After a successful push, you can check the AWS Management Console (Amazon ECR service) to see the new image appear.

![Image list in ECR Repository](/images/5-Workshop/5.5-docker-image/3-ecr-repository-images.png?featherlight=false&width=90pc)

You can also click on the image digest to view details:

![Docker Image Details on ECR](/images/5-Workshop/5.5-docker-image/4-ecr-image-details.png?featherlight=false&width=90pc)

Next, we will move to **[5.6. ECS Cluster & Task Definition](../5.6-ecs-cluster/)** to configure the container runtime environment for Playwright.
