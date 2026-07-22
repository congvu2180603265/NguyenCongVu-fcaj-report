---
title : "Amazon ECS"
date : 2026-07-10
weight : 6
chapter : false
pre : " <b> 5.13.6. </b> "
---

#### Delete Amazon ECS

Before deleting the ECS cluster, you must first delete the service running inside it, then delete the task definition.

1. Go to **Amazon ECS Console** → **Clusters** → click on `playwright-cluster`. On the **Services** tab, confirm there are **0 active** services (the ECS service should have already been stopped). In the cluster detail page, click **Actions** → **Delete cluster**. A **Delete cluster playwright-cluster** dialog appears. Type `delete playwright-cluster` in the confirmation field and click **Delete**.
![ECS 1](/images/5-Workshop/5.13-cleanup/5.13.6-ecs/ecs1.png?featherlight=false&width=90pc)

2. The **Delete cluster playwright-cluster** progress dialog shows: **Service deletion** — No services to delete; **Container instance deregistration** — No container instances to deregister; **Cluster and stack deletion** — Successfully deleted `playwright-cluster`. Click **Close**.
![ECS 2](/images/5-Workshop/5.13-cleanup/5.13.6-ecs/ecs2.png?featherlight=false&width=90pc)

3. After closing the dialog, the cluster status changes to **Inactive**. Next, navigate to **Task definitions** in the left sidebar. Select the task definition `playwright-task-def` (Status: **Active**), then choose **Actions** → **Deregister**.
![ECS 3](/images/5-Workshop/5.13-cleanup/5.13.6-ecs/esc3.png?featherlight=false&width=90pc)

4. On the **Task definitions** page, click the task definition `playwright-task-def`. Select all revisions, then choose **Actions** → **Delete**.
![ECS 4](/images/5-Workshop/5.13-cleanup/5.13.6-ecs/esc4.png?featherlight=false&width=90pc)

5. Upon success, the **Task definitions (0)** list with Filter status **Inactive** shows **No task definitions**.
![ECS 5](/images/5-Workshop/5.13-cleanup/5.13.6-ecs/esc5.png?featherlight=false&width=90pc)
