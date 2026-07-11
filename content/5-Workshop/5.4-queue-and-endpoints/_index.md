---
title : "Queue & VPC Endpoints"
date : 2026-07-10
weight : 4
chapter : false
pre : " <b> 5.4. </b> "
---

#### Create an SQS Queue

**Step 1:** Access the **AWS Management Console**, search for **Simple Queue Service**, and select **Simple Queue Service**.

**Step 2:** Click **Create queue**.

![Create Queue](/images/5-Workshop/5.4-queue-and-endpoints/1-create-queue.png?featherlight=false&width=90pc)

**Step 3:** Select type **Standard**, name it `playwright-dlq`, leave the other settings unchanged.

![Create the Dead Letter Queue](/images/5-Workshop/5.4-queue-and-endpoints/2-create-dlq.png?featherlight=false&width=90pc)

**Step 4:** Click **Create queue** to save.

**Step 5:** Click **Create queue** again, name it `playwright-task-queue`.

![Create the playwright task queue](/images/5-Workshop/5.4-queue-and-endpoints/3-create-ptq.png?featherlight=false&width=90pc)

**Step 6:** Scroll down to the **Dead-letter queue** section, turn on **Enabled**.

**Step 7:** Select queue = `playwright-dlq`, **Maximum receives** = `3`.

![Attach the Dead Letter Queue to the Task Queue](/images/5-Workshop/5.4-queue-and-endpoints/4-attach-dlq.png?featherlight=false&width=90pc)

**Step 8:** Click **Create queue**.

**Step 9:** Check that the **Dead-letter queue** tab of `playwright-task-queue` correctly shows `playwright-dlq` attached.

#### Create Endpoint 1: S3 (Gateway type)

**Step 1:** Access the **AWS Management Console**, search for **VPC**, select **VPC**, and then click **Endpoints** in the left navigation pane. Click **Create endpoint**.

**Step 2:** Name tag: enter `playwright-endpoint-s3`.

**Step 3:** Type: keep **AWS services** (already selected by default).

**Step 4:** In the search box under Services, type: `com.amazonaws.ap-southeast-1.s3`.

![Search for the S3 service](/images/5-Workshop/5.4-queue-and-endpoints/5-s3-search.png?featherlight=false&width=90pc)

**Step 5:** The results table will show 2 rows with the same service name — tick the one whose **Type = Gateway** (don't select the Interface row below it).

**Step 6:** Scroll down to **Network settings → VPC**: click the "Select a VPC" dropdown, choose `vpc-0b31d28ff732f70c9` (displayed as `playwright-vpc`).

**Step 7:** After selecting the VPC, the Console will automatically show an additional **"Route tables"** section below (this section only appears after a VPC is selected, it's not a missing UI element) — tick the route table `playwright-private-rtb` (ID `rtb-08b47df38de10ba54`). *Required — if not ticked, S3 traffic won't go through the endpoint.*

**Step 8:** The **Additional settings** section (IP address type, DNS record IP type): leave at default, no need to change.

**Step 9:** The **Policy** section: leave at default (**Full access**).

**Step 10:** Click the orange **Create endpoint** button in the bottom-right corner.

![S3 endpoint created](/images/5-Workshop/5.4-queue-and-endpoints/6-s3-created.png?featherlight=false&width=90pc)

---

#### Create Endpoint 2: ECR API (Interface type)

*Fargate needs this endpoint for authentication when pulling the Docker image.*

**Step 1:** Go to **VPC Console → Endpoints**, click **Create endpoint**.

**Step 2:** Name tag: enter `playwright-endpoint-ecr-api`.

**Step 3:** Search for service: `com.amazonaws.ap-southeast-1.ecr.api`.

![Search for the ECR API service](/images/5-Workshop/5.4-queue-and-endpoints/7-ecr-api-search.png?featherlight=false&width=90pc)

**Step 4:** Type: **Interface**.

**Step 5:** VPC: `vpc-0b31d28ff732f70c9`.

**Step 6:** Subnets: select `subnet-0eb315a2b47f1f1e0`.

**Step 7:** Security group: uncheck the default SG, tick `sg-05517c073af790abc`.

**Step 8:** Enable DNS name: keep it checked (required, so services automatically resolve through the endpoint instead of the internet).

**Step 9:** Click **Create endpoint**.

![ECR API endpoint created](/images/5-Workshop/5.4-queue-and-endpoints/8-ecr-api-created.png?featherlight=false&width=90pc)

---

#### Create Endpoint 3: ECR DKR (Interface type)

*Fargate needs this endpoint to pull the actual Docker image layers.*

**Step 1:** Go to **VPC Console → Endpoints**, click **Create endpoint**.

**Step 2:** Name tag: enter `playwright-endpoint-ecr-dkr`.

**Step 3:** Search for service: `com.amazonaws.ap-southeast-1.ecr.dkr`.

![Search for the ECR DKR service](/images/5-Workshop/5.4-queue-and-endpoints/9-ecr-dkr-search.png?featherlight=false&width=90pc)

**Step 4:** Type: **Interface**.

**Step 5:** VPC: `vpc-0b31d28ff732f70c9`.

**Step 6:** Subnets: select `subnet-0eb315a2b47f1f1e0`.

**Step 7:** Security group: uncheck the default SG, tick `sg-05517c073af790abc`.

**Step 8:** Enable DNS name: keep it checked.

**Step 9:** Click **Create endpoint**.

![ECR DKR endpoint created](/images/5-Workshop/5.4-queue-and-endpoints/10-ecr-dkr-created.png?featherlight=false&width=90pc)

---

#### Create Endpoint 4: CloudWatch Logs (Interface type)

**Step 1:** Go to **VPC Console → Endpoints**, click **Create endpoint**.

**Step 2:** Name tag: enter `playwright-endpoint-logs`.

**Step 3:** Search for service: `com.amazonaws.ap-southeast-1.logs`.

![Search for the CloudWatch Logs service](/images/5-Workshop/5.4-queue-and-endpoints/11-logs-search.png?featherlight=false&width=90pc)

**Step 4:** Type: **Interface**.

**Step 5:** VPC: `vpc-0b31d28ff732f70c9`.

**Step 6:** Subnets: `subnet-0eb315a2b47f1f1e0`.

**Step 7:** Security group: `sg-05517c073af790abc`.

**Step 8:** Enable DNS name: keep it checked.

**Step 9:** Click **Create endpoint**.

![CloudWatch Logs endpoint created](/images/5-Workshop/5.4-queue-and-endpoints/12-logs-created.png?featherlight=false&width=90pc)

---

#### Verify Private DNS for the Interface Endpoints

**Step 1:** After creating all 3 Interface Endpoints (ECR API, ECR DKR, CloudWatch Logs), click into each one.

**Step 2:** Open the **Details** tab.

**Step 3:** Check that **Private DNS names enabled** = **Yes**. If it's No, click **Actions → Modify private DNS name** to enable it.

![Check Private DNS names enabled - api](/images/5-Workshop/5.4-queue-and-endpoints/13-check-private-dns-api.png?featherlight=false&width=90pc ) 
![Check Private DNS names enabled - dkr](/images/5-Workshop/5.4-queue-and-endpoints/14-check-private-dns-dkr.png?featherlight=false&width=90pc)
![Check Private DNS names enabled - logs](/images/5-Workshop/5.4-queue-and-endpoints/15-check-private-dns-logs.png?featherlight=false&width=90pc)

#### Verification

**Step 1:** Go to **VPC Console → Endpoints**, confirm the list correctly shows **4 endpoints**: `playwright-endpoint-s3`, `playwright-endpoint-ecr-api`, `playwright-endpoint-ecr-dkr`, `playwright-endpoint-logs`.

**Step 2:** All of them are in **Available** status.

![Check available status](/images/5-Workshop/5.4-queue-and-endpoints/16-available-check.png?featherlight=false&width=90pc)

**Step 3:** Go to **VPC Console → Route Tables** → select `playwright-private-rtb` (`rtb-08b47df38de10ba54`) → **Routes** tab, confirm there is a new route in prefix list form `pl-xxxxx` pointing to the S3 Endpoint just created.

![Route Table has a route to the S3 Endpoint](/images/5-Workshop/5.4-queue-and-endpoints/17-route-table-check.png?featherlight=false&width=90pc)

---

The Queue and VPC Endpoints setup is complete. Next, we will move on to **[5.5. Docker Image](../5.5-docker-image/)** to build the Playwright container image.
