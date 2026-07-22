---
title : "AWS Secrets Manager"
date : 2026-07-10
weight : 9
chapter : false
pre : " <b> 5.13.9. </b> "
---

#### Delete AWS Secrets Manager

1. Go to **AWS Secrets Manager Console** → **Secrets**. In the list, click on the secret `playwright/openai-api-key` to open its detail page.
![Secrets Manager 1](/images/5-Workshop/5.13-cleanup/5.13.9-secrets-manager/secrets-manager1.png?featherlight=false&width=90pc)

2. On the secret detail page of `playwright/openai-api-key`, click **Actions** → **Delete secret**. A **Disable secret and schedule deletion** dialog appears, noting that Secrets Manager requires a minimum waiting period of 7 days before deleting a secret. Set the **Waiting period** to `7` days and click **Schedule deletion**.
![Secrets Manager 2](/images/5-Workshop/5.13-cleanup/5.13.9-secrets-manager/secrets-manager2.png?featherlight=false&width=90pc)

3. The secret detail page now displays an informational banner: **"This secret has been scheduled for deletion."** The **Secret details** panel shows **Deleted on: 7/15/2026** confirming the deletion is scheduled.
![Secrets Manager 3](/images/5-Workshop/5.13-cleanup/5.13.9-secrets-manager/secrets-manager3.png?featherlight=false&width=90pc)
