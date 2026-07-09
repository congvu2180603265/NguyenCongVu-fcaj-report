---
title : "Create VPC"
date : 2024-01-01 
weight : 2
chapter : false
pre : " <b> 5.2.2. </b> "
---

#### 1. Create Virtual Private Cloud (VPC)

In this section, we will build the network infrastructure from scratch. The first step is to create an isolated Virtual Private Cloud (VPC).

**Step 1:** Access the **VPC Dashboard** on the AWS Console and click the **Create VPC** button.

**Step 2:** On the creation interface, configure the following basic parameters:
- **Resources to create:** Select **VPC only**.
- **Name tag:** Enter a name for the VPC (e.g., `playwright-vpc`).
- **IPv4 CIDR block:** Select *IPv4 CIDR manual input* and enter the network range `10.0.0.0/16`.

![VPC Top Configuration](/images/5-Workshop/5.2-Prerequisite/5.2.2-create-vpc/2-vpc-name-cidr.png?featherlight=false&width=90pc)

**Step 3:** Scroll down to the **Tags** section (the system has automatically filled this from the previous step). Leave the remaining default configurations unchanged and click the orange **Create VPC** button.

![VPC Bottom Configuration](/images/5-Workshop/5.2-Prerequisite/5.2.2-create-vpc/3-vpc-subnets.png?featherlight=false&width=90pc)

**Step 4:** When the green success notification appears, congratulations, you have successfully created the VPC network frame.

#### 2. Subnet division (Subnets)

After creating the VPC, we need to divide it into Subnets. Our system requires at least 1 Public Subnet (for Internet communication) and 1 Private Subnet (to securely run E2E tasks).

**Step 1:** On the left menu, select **Subnets** and click the **Create subnet** button in the top right corner.

**Step 2:** On the configuration screen, select your newly created VPC (`playwright-vpc`). Under **Subnet settings**, fill in the following information:
- **Subnet name:** `playwright-public-subnet`
- **Availability Zone:** Select `Asia Pacific (Singapore) / apse1-az2 (ap-southeast-1a)`
- **IPv4 subnet CIDR block:** Enter `10.0.1.0/24`
- **Tags:** The system automatically fills the Key with `Name` and Value with `playwright-public-subnet`.

![Top Configuration](/images/5-Workshop/5.2-Prerequisite/5.2.2-create-vpc/2-subnet-top-config.png?featherlight=false&width=90pc)

**Step 3:** Scroll to the bottom, double-check the parameters, and click the orange **Create subnet** button.

![Bottom Configuration](/images/5-Workshop/5.2-Prerequisite/5.2.2-create-vpc/3-subnet-bottom-config.png?featherlight=false&width=90pc)

**Step 4: Configure Private Subnet**
The process of creating a Private Subnet is exactly the same as the Public Subnet. Click **Create subnet** again to create the second subnet with the following parameters:
- **Subnet name:** `playwright-private-subnet`
- **Availability Zone:** Select `Asia Pacific (Singapore) / apse1-az1 (ap-southeast-1b)` (or any AZ different from the public subnet)
- **IPv4 subnet CIDR block:** Enter `10.0.2.0/24`

#### 3. Create Internet Gateway (IGW)

For the Public Subnet to communicate with the Internet, we need an **Internet Gateway**.

**Step 1:** On the left menu, select **Internet gateways** and click the **Create internet gateway** button.

**Step 2:** On the creation screen, enter `playwright-igw` in the **Name tag** field and click the **Create internet gateway** button.

![Create Internet Gateway](/images/5-Workshop/5.2-Prerequisite/5.2.2-create-vpc/6-igw-create.png?featherlight=false&width=90pc)

**Step 3:** After creation, the state of the IGW is **Detached**. Click the **Actions** button in the top right corner and select **Attach to VPC**.

**Step 4:** On the **Attach to VPC** screen, click the **Available VPCs** field, select `vpc-... | playwright-vpc` from the dropdown list, and click the **Attach internet gateway** button.

![Attach to VPC](/images/5-Workshop/5.2-Prerequisite/5.2.2-create-vpc/9-igw-attach-vpc.png?featherlight=false&width=90pc)

#### 4. Allocate Elastic IP and Create NAT Gateway

The Private Subnet cannot access the Internet directly. To allow resources in the Private Subnet (like ECS Fargate) to download packages or communicate outbound, we need a **NAT Gateway** placed in the Public Subnet, and this NAT Gateway requires a static IP address (**Elastic IP**).

**Step 1: Allocate Elastic IP**
- From the left menu, select **Elastic IPs** (under Virtual private cloud) and click the **Allocate Elastic IP address** button.
- On the configuration screen, keep the default options:
  - **Network border group:** `ap-southeast-1`
  - **Public IPv4 address pool:** `Amazon's pool of IPv4 addresses`
- Scroll to the bottom and click the **Allocate** button.

![Allocate Elastic IP](/images/5-Workshop/5.2-Prerequisite/5.2.2-create-vpc/11-elastic-ip-config.png?featherlight=false&width=90pc)

**Step 2: Create NAT Gateway**
- Return to the left menu, select **NAT gateways**, and click the **Create NAT gateway** button.
- On the **NAT gateway settings** screen, fill in the exact parameters:
  - **Name:** `playwright-nat`
  - **Availability mode:** Select `Zonal`
  - **Subnet:** Click the field and select the Public Subnet you created (e.g., `subnet-... (playwright-public-subnet)`). **Crucial note:** You must select the Public Subnet, not the Private Subnet.
  - **Connectivity type:** `Public`
  - **Elastic IP allocation ID:** Click the field and select the Elastic IP address allocated in Step 1 (e.g., `eipalloc-...`).
- Scroll down and click the orange **Create NAT gateway** button.

![Create NAT Gateway](/images/5-Workshop/5.2-Prerequisite/5.2.2-create-vpc/12-nat-gateway-config.png?featherlight=false&width=90pc)

#### 5. Configure Route Tables

A Route Table acts like a traffic sign, deciding where network traffic should go. We need to configure 2 Route Tables: one for the Public Subnet (routing to the Internet) and one for the Private Subnet (routing to the NAT Gateway).

**Part 1: Configure Public Route Table**
1. Select **Route tables** from the left menu. You will see a default Route Table created along with the VPC. Rename it to `playwright-public-rtb` for easy identification.
2. Select this Route Table, switch to the **Routes** tab in the lower half of the screen, and click **Edit routes**.
3. Click **Add route**:
   - **Destination:** Enter `0.0.0.0/0`
   - **Target:** Select **Internet Gateway** and point it to the IGW you just created (e.g., `igw-...`).
4. Click **Save changes**.

![Route to Internet Gateway](/images/5-Workshop/5.2-Prerequisite/5.2.2-create-vpc/13-public-rtb-route-igw.png?featherlight=false&width=90pc)

*(Note: By default, the VPC might implicitly associate subnets with this Main Route Table. To be explicit, switch to the **Subnet associations** tab, click **Edit subnet associations**, and select `playwright-public-subnet`, similar to the Private Subnet process below).*

**Part 2: Create and Configure Private Route Table**
1. Still on the **Route tables** screen, click the **Create route table** button.
2. Enter `playwright-private-rtb` for the **Name** and select the `vpc-... (playwright-vpc)` VPC, then click the orange **Create route table** button.

![Create Private Route Table](/images/5-Workshop/5.2-Prerequisite/5.2.2-create-vpc/14-private-rtb-create.png?featherlight=false&width=90pc)

3. After creation, on the `playwright-private-rtb` details page, switch to the **Subnet associations** tab and click **Edit subnet associations**.
4. Check the box for `playwright-private-subnet` and click **Save associations**.

![Associate Private Subnet](/images/5-Workshop/5.2-Prerequisite/5.2.2-create-vpc/15-private-rtb-association.png?featherlight=false&width=90pc)

5. Next, switch to the **Routes** tab and click **Edit routes**.
6. Click **Add route**:
   - **Destination:** Enter `0.0.0.0/0`
   - **Target:** Select **NAT Gateway** and point it to the NAT Gateway you just created (`nat-... (playwright-nat)`).
7. Click **Save changes**.

![Route to NAT Gateway](/images/5-Workshop/5.2-Prerequisite/5.2.2-create-vpc/16-private-rtb-route-nat.png?featherlight=false&width=90pc)

#### 6. Configure Security Groups

To allow services inside the VPC to communicate securely, we need to set up Security Groups (SGs). We need 3 SGs: for Lambda, for Fargate, and for VPC Endpoints.
**Important note on ordering:** You must create the SGs for Lambda and Fargate first, before creating the Endpoint SG, because the Endpoint SG needs to reference the other two in its Inbound rules.

**Part 1: Create SGs for Lambda and Fargate (Source SGs)**
1. Select **Security Groups** from the left menu and click **Create security group**.
2. We will create 2 SGs one by one with identical configurations (except for the name):
   - **Lambda SG:**
     - **Security group name:** `playwright-sg-lambda`
     - **Description:** `security group for lambda function`
   - **Fargate SG:** (Repeat the Create step after creating Lambda SG)
     - **Security group name:** `playwright-sg-fargate`
     - **Description:** `security group for fargate`
3. For both SGs, ensure the **VPC** is set to `playwright-vpc`.
4. **Inbound rules:** No rules are needed (delete any default rules).
5. **Outbound rules:** Keep the default `All traffic` to `0.0.0.0/0`.
6. Click **Create security group**.

![Create Lambda SG](/images/5-Workshop/5.2-Prerequisite/5.2.2-create-vpc/17-sg-lambda-create.png?featherlight=false&width=90pc)

**Part 2: Create SG for VPC Endpoints (Target SG)**
1. Click **Create security group** again.
2. **Security group name:** `playwright-sg-endpoint`
3. **Description:** `securitygroup for endpoints`
4. Select the `playwright-vpc`.
5. **Inbound rules:** We need to allow Lambda and Fargate to call the Endpoints via HTTPS. Add 2 rules:
   - **Type:** `HTTPS` (Automatically sets to Port 443).
   - **Source:** Select `Custom`, click the search box and select `playwright-sg-lambda`.
   - Click **Add rule** to add a second identical rule, but select `playwright-sg-fargate` as the Source.

![Endpoint SG Inbound Configuration](/images/5-Workshop/5.2-Prerequisite/5.2.2-create-vpc/18-sg-endpoint-inbound.png?featherlight=false&width=90pc)

6. **Outbound rules:** Keep the default `All traffic` to `0.0.0.0/0`.

![Default Outbound Configuration](/images/5-Workshop/5.2-Prerequisite/5.2.2-create-vpc/19-sg-outbound-default.png?featherlight=false&width=90pc)

7. Scroll down and click **Create security group**.

**Excellent!** You have completely built a standard VPC network infrastructure to safely run the E2E Testing system.
