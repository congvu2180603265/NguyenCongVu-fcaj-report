---
title : "Amazon VPC"
date : 2026-07-10
weight : 13
chapter : false
pre : " <b> 5.13.13. </b> "
---

#### Delete Amazon VPC

Before deleting the VPC, you must first delete the 4 VPC Endpoints attached to it. Do **NOT** delete the Default VPC — only delete `playwright-vpc`.

1. Navigate to **VPC Console** → **Endpoints**. In the **Endpoints (4/4)** list, select all 4 endpoints: `playwright-endpoint-s3` (Gateway), `playwright-endpoint-ecr-api` (Interface), `playwright-endpoint-ecr-dkr` (Interface), and `playwright-endpoint-logs` (Interface). Then choose **Actions** → **Delete VPC endpoints**.
![VPC 1](/images/5-Workshop/5.13-cleanup/5.13.13-vpc/vpc3.png?featherlight=false&width=90pc)

2. A **Delete endpoints** dialog appears, listing all 4 endpoints that will be deleted permanently. Type `delete` in the confirmation field and click **Delete**.
![VPC 2](/images/5-Workshop/5.13-cleanup/5.13.13-vpc/vpc4.png?featherlight=false&width=90pc)

3. The system displays a green banner **"Successfully deleted endpoints"** with the IDs of all 4 deleted endpoints. The remaining Interface endpoints show status **Deleting**.
![VPC 3](/images/5-Workshop/5.13-cleanup/5.13.13-vpc/vpc5.png?featherlight=false&width=90pc)

4. After all endpoints are deleted, the **Endpoints** list shows **No endpoint found**.
![VPC 4](/images/5-Workshop/5.13-cleanup/5.13.13-vpc/vpc6.png?featherlight=false&width=90pc)

5. Now navigate to **VPC Console** → **Your VPCs**. Identify the correct `playwright-vpc` to delete (one that is NOT the Default VPC). Click on the VPC named `playwright-vpc` and confirm that its State is **Available**.
![VPC 5](/images/5-Workshop/5.13-cleanup/5.13.13-vpc/vpc2.png?featherlight=false&width=90pc)

6. With `playwright-vpc` selected, choose **Actions** → **Delete VPC**. A **Delete VPC** dialog appears showing the VPC `playwright-vpc` (State: Available) that will be deleted, along with 8 associated resources that will also be deleted (including `playwright-igw`, `playwright-public-rtb`, `playwright-private-rtb`, and security groups). Type `delete` in the confirmation field and click **Delete**.
![VPC 6](/images/5-Workshop/5.13-cleanup/5.13.13-vpc/vpc7.png?featherlight=false&width=90pc)

7. Upon success, the system displays a green banner **"You successfully deleted vpc-0b31d28ff732f70c9 / playwright-vpc and 8 other resources."** The **Your VPCs** list shows **No VPCs found in this Region**.
![VPC 7](/images/5-Workshop/5.13-cleanup/5.13.13-vpc/vpc8.png?featherlight=false&width=90pc)
