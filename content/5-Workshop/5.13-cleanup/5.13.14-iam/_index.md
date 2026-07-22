---
title : "IAM"
date : 2026-07-10
weight : 14
chapter : false
pre : " <b> 5.13.14. </b> "
---

#### Delete IAM Roles

Delete the 4 IAM roles that were created specifically for this workshop.

1. Go to **IAM Console** → **Access Management** → **Roles**. In the **Roles (4/16)** list, select the 4 roles with the `playwright-` prefix: `playwright-ecs-execution-role`, `playwright-ecs-task-role`, `playwright-lambda-role`, and `playwright-postprocessing-role`. Then click the **Delete** button.
![IAM 1](/images/5-Workshop/5.13-cleanup/5.13.14-iam/IAM1.png?featherlight=false&width=90pc)

2. A **Delete 4 roles?** dialog appears, confirming the permanent deletion of the 4 roles along with all their inline policies and attached instance profiles. The dialog lists: `playwright-lambda-role`, `playwright-ecs-execution-role`, `playwright-ecs-task-role`, and `playwright-postprocessing-role`. Type `delete` in the confirmation field and click **Delete**.
![IAM 2](/images/5-Workshop/5.13-cleanup/5.13.14-iam/IAM2.png?featherlight=false&width=90pc)

3. Upon success, the system displays a green banner **"Roles deleted."** The **Roles (12)** list no longer contains any `playwright-` prefixed roles, confirming all workshop-related IAM roles have been removed.
![IAM 3](/images/5-Workshop/5.13-cleanup/5.13.14-iam/IAM3.png?featherlight=false&width=90pc)
