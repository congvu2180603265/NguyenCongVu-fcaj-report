---
title : "ECS Cluster & Task Definition"
date : 2026-06-17
weight : 6
chapter : false
pre : " <b> 5.6. </b> "
---

#### Create an ECS Cluster and Task Definition for Playwright Runner

In this section, you will create an Amazon ECS Cluster and configure a Task Definition — which defines which Docker image is used, how many resources are allocated, and which IAM role is applied when ECS Fargate runs the Playwright test tasks.

#### 1. Create the ECS Cluster

A Cluster is a logical grouping of compute resources (in this case, Fargate) to run your tasks.

**Step 1:** Sign in to the AWS Management Console, search for and select the **Elastic Container Service (ECS)** service.

**Step 2:** In the left-hand menu, select **Clusters**, then click the **Create cluster** button.

**Step 3:** Configure the Cluster:
- **Cluster name**: Enter `playwright-cluster`.
- Under **Infrastructure**, make sure **Fargate only** (Serverless) is selected. You can leave the other settings at default.

![Cluster creation configuration](/images/5-Workshop/5.6-ecs-cluster/1-create-cluster.png?featherlight=false&width=90pc)

**Step 4:** Scroll to the bottom and click **Create**.

Once created, you'll see a success notification and the `playwright-cluster` cluster will appear in the list.

![ECS Cluster created successfully](/images/5-Workshop/5.6-ecs-cluster/2-cluster-created.png?featherlight=false&width=90pc)

Clicking the cluster name shows the cluster overview (no services or tasks running yet).

![ECS Cluster details](/images/5-Workshop/5.6-ecs-cluster/3-cluster-details.png?featherlight=false&width=90pc)

---

#### 2. Create the Task Definition

A Task Definition is like a blueprint for your application. It tells ECS how to run the Docker container.

**Step 1:** In the left-hand menu of the ECS Console, select **Task definitions**.

**Step 2:** Click the **Create new task definition** button and select **Create new task definition**.

**Step 3:** Configure the Task definition configuration:
- **Task definition family**: Enter `playwright-task-def`.

**Step 4:** Configure Infrastructure requirements:
- **Launch type**: Select **AWS Fargate**.
- **Operating system/Architecture**: Select **Linux/X86_64**.
- **Task size**:
  - CPU: `1 vCPU`
  - Memory: `2 GB`
- **Task role**: Open the dropdown and select the `playwright-ecs-task-role` created earlier.
- **Task execution role**: Select the `playwright-ecs-execution-role` created earlier.

**Step 5:** Configure Container - 1:
- **Name**: Enter `playwright-runner` (or a name of your choice).
- **Image URI**: Paste the URI of the Docker image you pushed to ECR in section 5.5 (e.g., `238337501662.dkr.ecr.ap-southeast-1.amazonaws.com/playwright-runner:latest`).
- **Port mappings**: You can remove the default port 80 mapping, since this task only runs the test script and then exits — it doesn't need to listen for incoming connections (it's not running a web server).

**Step 6:** Scroll to the bottom and click **Create**.

Once created successfully, you'll see a message confirming the `playwright-task-def:1` Task Definition was created, along with the CPU, Memory, and Roles correctly assigned.

![Task Definition created successfully](/images/5-Workshop/5.6-ecs-cluster/4-task-def-created.png?featherlight=false&width=90pc)

---

Next, we will move on to **[5.7. Lambda Functions](../5.7-lambda-functions/)** to deploy the orchestration layer that invokes this ECS Task.
