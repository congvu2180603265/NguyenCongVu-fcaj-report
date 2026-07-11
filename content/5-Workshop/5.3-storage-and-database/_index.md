---
title : "Storage & Database"
date : 2026-07-10
weight : 3
chapter : false
pre : " <b> 5.3. </b> "
---

#### Create DynamoDB tables to store test history and error logs

The system uses 2 DynamoDB tables: one storing the history of each test run, and one storing system errors when a message lands in the Dead Letter Queue.

**Step 1:** Access the **AWS Management Console**, search for **DynamoDB**, and select **DynamoDB**.

![Access DynamoDB Console](/images/5-Workshop/5.3-storage-and-database/1-access-dynamodb-console.png?featherlight=false&width=90pc)

**Step 2:** Click **Create table**.

![Click Create table](/images/5-Workshop/5.3-storage-and-database/2-create-table-button.png?featherlight=false&width=90pc)

**Step 3:** Enter **Table name** = `playwright-test-history`.

**Step 4:** Enter **Partition key** = `task_id`, type **String**.

![Create the playwright-test-history table](/images/5-Workshop/5.3-storage-and-database/3-create-test-history-table.png?featherlight=false&width=90pc)

**Step 5:** Leave the other settings as default, click **Create table**.

![Create table playwright-test-history](/images/5-Workshop/5.3-storage-and-database/4-create-test-history-table.png?featherlight=false&width=90pc)

**Step 6:** Click **Create table** again to create the second table.

**Step 7:** Enter **Table name** = `playwright-error-log`.

**Step 8:** Enter **Partition key** = `error_id`, type **String**.

![Create the playwright-error-log table](/images/5-Workshop/5.3-storage-and-database/5-create-error-log-table.png?featherlight=false&width=90pc)

**Step 9:** Click **Create table**.

![Create table playwright-error-log](/images/5-Workshop/5.3-storage-and-database/6-create-error-log-table.png?featherlight=false&width=90pc)

**Step 10:** Click **Create table** a third time, name it `playwright-email-config`, Partition key = `email_address`, type **String**, click **Create table**.

![Create the playwright-email-config table](/images/5-Workshop/5.3-storage-and-database/11-create-email-config-table.png?featherlight=false&width=90pc)

**Step 11:** Click **Create table** a fourth time, name it `playwright-test-suites`, Partition key = `suite_id`, type **String**, click **Create table**.

![Create the playwright-test-suites table](/images/5-Workshop/5.3-storage-and-database/12-create-test-suites-table.png?featherlight=false&width=90pc)

**Step 12:** Wait for all 4 tables to switch to **Active** status.

![All tables in Active status](/images/5-Workshop/5.3-storage-and-database/7-tables-active.png?featherlight=false&width=90pc)

*(Note: No need to create any other fields such as `target_url`, `status`, `report_url`, etc. — DynamoDB is NoSQL and automatically picks up fields when the application writes data, with no need to declare a rigid schema upfront.)*

#### Grant permissions for the Fargate container to write directly to DynamoDB

Since the Fargate container writes the `running/success/failed` status directly to the `playwright-test-history` table (bypassing Lambda), the Task Role needs additional permissions.

**Step 13:** Access the **AWS Management Console**, search for **IAM**, select **IAM**, and search for the role `playwright-ecs-task-role` under **Roles**.

![Find the role `playwright-ecs-task-role`](/images/5-Workshop/5.3-storage-and-database/8-search-playwright-ecs-task-role.png?featherlight=false&width=90pc)

**Step 14:** Click **Add permissions → Create inline policy**.

![Create an inline policy](/images/5-Workshop/5.3-storage-and-database/9-create-inline-policy.png?featherlight=false&width=90pc)

**Step 15:** Select the **JSON** tab, paste the following content:
```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": ["dynamodb:PutItem", "dynamodb:UpdateItem"],
    "Resource": "arn:aws:dynamodb:ap-southeast-1:<account-id>:table/playwright-test-history"
  }]
}
```

**Step 16:** Replace `<account-id>` with the real Account ID (12 digits, shown in the top-right corner of the Console).

**Step 17:** Name the policy, e.g. `DynamoDBWriteAccess`, click **Create policy**.

![Policy attached to the role](/images/5-Workshop/5.3-storage-and-database/10-inline-policy-attached.png?featherlight=false&width=90pc)

#### Verification

- All 4 tables show **Active** status in the Console.
- `playwright-ecs-task-role` shows the inline policy `DynamoDBWriteAccess` in the Permissions tab.
- After running a test: go to the `playwright-test-history` table → **Explore table items** tab → see a record with `status`, `report_url`, `ai_summary` all populated.

---

The Storage & Database setup is complete. Next, we will move on to **[5.4. Queue & VPC Endpoints](../5.4-queue-and-endpoints/)** to create the SQS queue and configure private VPC Endpoints.
