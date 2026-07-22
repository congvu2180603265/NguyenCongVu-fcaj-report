---
title : "Amazon DynamoDB"
date : 2026-07-10
weight : 11
chapter : false
pre : " <b> 5.13.11. </b> "
---

#### Delete Amazon DynamoDB

1. Go to **Amazon DynamoDB Console** → **Tables**. In the **Tables (4/4)** list, select all 4 tables: `playwright-email-config`, `playwright-error-log`, `playwright-test-history`, and `playwright-test-suites`. Then click the **Delete** button.
![DynamoDB 1](/images/5-Workshop/5.13-cleanup/5.13.11-dynamodb/DynamoDB1.png?featherlight=false&width=90pc)

2. A **Delete tables** dialog appears, warning that deleting 4 tables in *Asia Pacific (Singapore)* cannot be undone and data cannot be retrieved. Optionally check **Delete all CloudWatch alarms for the 4 tables selected**. Type `confirm` in the confirmation field and click **Delete**.
![DynamoDB 2](/images/5-Workshop/5.13-cleanup/5.13.11-dynamodb/DynamoDB2.png?featherlight=false&width=90pc)

3. The system displays a green banner: **"The request to delete the 'playwright-email-config, playwright-error-log, playwright-test-history, playwright-test-suites' tables has been submitted successfully."** The **Tables (4)** list shows all 4 tables with status **Deleting**.
![DynamoDB 3](/images/5-Workshop/5.13-cleanup/5.13.11-dynamodb/DynamoDB3.png?featherlight=false&width=90pc)

4. After the deletion completes, the **Tables (0)** list displays **"You have no tables in this account in this AWS Region."**
![DynamoDB 4](/images/5-Workshop/5.13-cleanup/5.13.11-dynamodb/DynamoDB4.png?featherlight=false&width=90pc)
