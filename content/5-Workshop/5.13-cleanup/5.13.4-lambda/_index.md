---
title : "AWS Lambda"
date : 2026-07-10
weight : 4
chapter : false
pre : " <b> 5.13.4. </b> "
---

#### Delete AWS Lambda

1. Go to **Lambda Console** → **Functions**. Select all 4 functions with the `playwright-` prefix (e.g., `playwright-lambda-trigger`, `playwright-postprocessing`, `playwright-runner`, `playwright-scheduler`), then choose **Actions** → **Delete**. A **Delete functions** dialog appears, confirming the permanent deletion of 4 functions. Type `confirm` in the confirmation field and click **Delete**.
![Lambda 1](/images/5-Workshop/5.13-cleanup/5.13.4-lambda/lamda1.png?featherlight=false&width=90pc)

2. Upon success, the system displays a green banner **"Successfully deleted 4 functions"** and the **Functions (0)** list shows **There is no data to display**.
![Lambda 2](/images/5-Workshop/5.13-cleanup/5.13.4-lambda/lamda2.png?featherlight=false&width=90pc)
