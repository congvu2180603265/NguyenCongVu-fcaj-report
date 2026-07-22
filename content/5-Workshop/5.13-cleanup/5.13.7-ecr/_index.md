---
title : "Amazon ECR"
date : 2026-07-10
weight : 7
chapter : false
pre : " <b> 5.13.7. </b> "
---

#### Delete Amazon ECR

1. Go to **Amazon ECR Console** → **Private registry** → **Repositories**. In the **Private repositories (1)** list, select the repository `playwright-runner`, then click the **Delete** button.
![ECR 1](/images/5-Workshop/5.13-cleanup/5.13.7-ecr/ecr1.png?featherlight=false&width=90pc)

2. A **Delete playwright-runner** dialog appears asking: "Are you sure you want to delete private repository: `playwright-runner`?". Type `delete` in the confirmation field and click **Delete**.
![ECR 2](/images/5-Workshop/5.13-cleanup/5.13.7-ecr/ecr2.png?featherlight=false&width=90pc)

3. Upon success, the system displays a green banner **"Successfully deleted private repository, playwright-runner"** and the **Private repositories** list shows **No repositories** — **No repositories were found**.
![ECR 3](/images/5-Workshop/5.13-cleanup/5.13.7-ecr/ecr3.png?featherlight=false&width=90pc)
