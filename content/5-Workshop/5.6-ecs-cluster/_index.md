---
title : "ECS Cluster & Task Definition"
date : 2026-07-10
weight : 6
chapter : false
pre : " <b> 5.6. </b> "
---

#### Create an ECS Cluster and Task Definition for Playwright Runner

In this section, you will create an Amazon ECS Cluster and configure a Task Definition — which defines which Docker image is used, how much resources to allocate, and which IAM role is applied when ECS Fargate runs Playwright test tasks.

### 1. Create an ECS Cluster

A cluster is a logical grouping of compute resources (in this case, Fargate) to run your tasks.

**Step 1:** Log in to the AWS Management Console, search for and select the **Elastic Container Service (ECS)** service.

**Step 2:** On the left navigation menu, select **Clusters**, then click the **Create cluster** button.

**Step 3:** Configure the Cluster:

- **Cluster name**: Enter `playwright-cluster`.
- In the **Infrastructure** section, ensure the **AWS Fargate (serverless)** option is selected. You can leave the other settings as default.

![Configure ECS Cluster creation](/images/5-Workshop/5.6-ecs-cluster/1-create-cluster.png?featherlight=false&width=90pc)

**Step 4:** Scroll to the bottom and click **Create**.

Once created, you will see a success message and the `playwright-cluster` will appear in the list.

![ECS Cluster successfully created](/images/5-Workshop/5.6-ecs-cluster/2-cluster-created.png?featherlight=false&width=90pc)

Clicking on the cluster name will show you the cluster overview (with no services or tasks running yet).

![ECS Cluster details](/images/5-Workshop/5.6-ecs-cluster/3-cluster-details.png?featherlight=false&width=90pc)

---

### 2. Create a Task Definition

A Task Definition is like a blueprint for your application. It tells ECS how to run your Docker container.

**Step 1:** On the left navigation menu of the ECS Console, select **Task definitions**.

**Step 2:** Click the **Create new task definition** button and select **Create new task definition**.

**Step 3:** Task definition configuration:

- **Task definition family**: Enter `playwright-task-def`.

**Step 4:** Infrastructure requirements:

- **Launch type**: Select **AWS Fargate**.
- **Operating system/Architecture**: Select **Linux/X86_64**.
- **Task size**:
  - CPU: `1 vCPU`
  - Memory: `2 GB`
- **Task role**: Open the dropdown and select the `playwright-ecs-task-role` created in the previous section.
- **Task execution role**: Select the `playwright-ecs-execution-role` created in the previous section.

**Step 5:** Container - 1 configuration:

- **Name**: Enter `playwright-container`.
- **Image URI**: Paste the URI of the Docker image you pushed to ECR in section 5.5 (Example: `238337501662.dkr.ecr.ap-southeast-1.amazonaws.com/playwright-runner:latest`).
- **Port mappings**: You can remove the default port 80 (Remove) because this task only runs a test script and exits, so there is no need to listen for incoming connections (it does not run a web server).
- **Environment variables**: Click **Add environment variable**, enter `REPORT_BUCKET` for the Key, and the name of your S3 Bucket used for storing Reports for the Value.
- **Log collection**: Scroll down to the Log configuration section, and ensure that **Use log collection** is checked.

**Step 6:** Scroll down to the bottom and click **Create**.

After successful initialization, you will see a message indicating the Task definition `playwright-task-def:1` was successfully created, along with configuration parameters such as CPU, Memory, and the correctly assigned Roles.

![Task Definition successfully created](/images/5-Workshop/5.6-ecs-cluster/4-task-def-created.png?featherlight=false&width=90pc)

---

Next, we will move on to **[5.7. Lambda Functions](../5.7-lambda-functions/)** to deploy the orchestration layer that calls this ECS Task.
