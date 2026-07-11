---
title : "ECS Cluster & Task Definition"
date : 2026-07-10
weight : 6
chapter : false
pre : " <b> 5.6. </b> "
---

#### Create an ECS Cluster and Task Definition for Playwright Runner

In this section, you will create an Amazon ECS Cluster and configure a Task Definition that tells ECS Fargate which Docker image to use, how many resources to allocate, and which IAM roles to apply when running Playwright tests.

{{% notice note %}}
Content for this section will be updated. Ensure the Docker image has been pushed to ECR (section 5.5) and the IAM roles `playwright-ecs-execution-role` and `playwright-ecs-task-role` are created (section 5.2.3) before proceeding.
{{% /notice %}}

---

Next, we will move on to **[5.7. Lambda Functions](../5.7-lambda-functions/)** to deploy the orchestration layer.
