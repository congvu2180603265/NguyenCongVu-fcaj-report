---
title : "Docker Image"
date : 2026-06-16
weight : 5
chapter : false
pre : " <b> 5.5. </b> "
---

#### Build and Push the Playwright Docker Image to Amazon ECR

In this section, you will build a Docker image containing the Playwright test scripts and push it to the Amazon ECR repository (`playwright-runner`) created in the previous section.

#### 1. Prepare the Dockerfile

First, make sure your project source directory has a `Dockerfile` to package Playwright. Below is a basic example `Dockerfile`:

```dockerfile
# Use the official Playwright image
FROM mcr.microsoft.com/playwright:v1.44.0-jammy

# Set the working directory
WORKDIR /app

# Copy package config files (package.json, package-lock.json)
COPY package*.json ./

# Install dependencies
RUN npm ci

# Copy all test source code into the container
COPY . .

# Default command when running the container (runs the test)
ENTRYPOINT ["node", "entrypoint.js"]
```

#### 2. Push the Image to ECR

You can copy the commands below directly, or view them in the AWS Console ECR by selecting the `playwright-runner` repository and clicking **"View push commands"**.

**Step 1: Authenticate the Docker client with Amazon ECR**

Get an authentication token and pass it to the Docker login command:

```bash
aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin 238337501662.dkr.ecr.ap-southeast-1.amazonaws.com
```

![ECR authentication succeeded](/images/5-Workshop/5.5-docker-image/1-docker-login.png?featherlight=false&width=90pc)


**Step 2: Build the Docker image**

From the folder containing the `Dockerfile` and the Playwright source code, run:

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

![Image pushed to ECR successfully](/images/5-Workshop/5.5-docker-image/2-docker-push.png?featherlight=false&width=90pc)


{{% notice tip %}}
**Tip:** Make sure `aws-cli` is installed and configured on your machine with sufficient ECR access (`AdministratorAccess` or `AmazonEC2ContainerRegistryFullAccess`) before running the commands above.
{{% /notice %}}

---

After a successful push, you can check the AWS Console (Amazon ECR service) to see the new image.

![List of images in the ECR repository](/images/5-Workshop/5.5-docker-image/3-ecr-repository-images.png?featherlight=false&width=90pc)

You can also click the image digest to view its details:

![Docker image details on ECR](/images/5-Workshop/5.5-docker-image/4-ecr-image-details.png?featherlight=false&width=90pc)

Next, we will move on to **[5.6. ECS Cluster & Task Definition](../5.6-ecs-cluster/)** to configure the container runtime environment for Playwright.
