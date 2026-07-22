---
title : "Amazon SQS"
date : 2026-07-10
weight : 8
chapter : false
pre : " <b> 5.13.8. </b> "
---

#### Delete Amazon SQS

There are 2 queues to delete: `playwright-dlq` and `playwright-task-queue`.

1. Go to **Amazon SQS Console** → **Queues**. In the **Queues (2)** list, select `playwright-dlq`, then click the **Delete** button. A **Delete queue** dialog appears, warning that this action cannot be undone. Type `confirm` in the confirmation field and click **Delete**.
![SQS 1](/images/5-Workshop/5.13-cleanup/5.13.8-sqs/sqs1.png?featherlight=false&width=90pc)

2. The system displays a green banner **"Queue playwright-dlq has been deleted successfully."** and the list returns to **Queues (1)** showing only `playwright-task-queue` remaining.
![SQS 2](/images/5-Workshop/5.13-cleanup/5.13.8-sqs/sqs2.png?featherlight=false&width=90pc)

3. Now select `playwright-task-queue`, then click the **Delete** button. The **Delete queue** dialog appears confirming deletion of `playwright-task-queue - contains 0 messages`. Type `confirm` and click **Delete**.
![SQS 3](/images/5-Workshop/5.13-cleanup/5.13.8-sqs/sqs3.png?featherlight=false&width=90pc)

4. The system displays a green banner **"Queue playwright-task-queue has been deleted successfully."** and the **Queues (0)** list shows **No queues** — **No queues available**.
![SQS 4](/images/5-Workshop/5.13-cleanup/5.13.8-sqs/sqs4.png?featherlight=false&width=90pc)
