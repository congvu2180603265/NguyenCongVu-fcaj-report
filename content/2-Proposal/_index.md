---
title: "Proposal"
date: 2026-06-19
weight: 2
chapter: false
pre: " <b> 2. </b> "
---


# Cloud-Native E2E Testing Platform  
## An AWS Serverless Solution for Automated End-to-End Testing and Intelligent Report Management.  

### 1. Executive Summary  
This platform is an automated End-to-End (E2E) website testing solution built to completely eliminate the need for QA engineers to manually trigger and monitor test runs whenever new code changes are deployed. Playwright executes inside Docker containers to simulate real user browser behavior (navigation, form inputs, assertion checks), followed by an AI summarization step that distills raw technical logs into human-readable email notifications.

The entire platform operates on AWS following an event-driven, serverless-first architecture: Amazon EventBridge triggers scheduled test executions, Amazon SQS decouples request ingestion from execution, an AWS Lambda function (Coordinator) provisions an isolated Amazon ECS Fargate task for each test run, and the task automatically terminates once report generation is complete. Consequently, the platform only incurs costs for the exact minutes tests are actually running, rather than paying for a 24/7 server sitting idle most of the time. Administrative Dashboard access is controlled via role-based access control (RBAC) across three distinct roles (Admin, QA/Tester, Developer) authenticated through Amazon Cognito.

### 2. Problem Statement  
**Current Challenges**  
Manual End-to-End testing requires QA testers to manually execute test scripts whenever code changes are deployed—an approach that fails to scale as application volume and test suite complexity grow. There is a lack of a centralized system to schedule test runs, track historical Pass/Fail trends, or automatically notify relevant stakeholders. Furthermore, maintaining a 24/7 server solely to stay ready for the next test execution incurs unnecessary operational waste, as the server remains idle for the majority of the time.

**Proposed Solution**  
The system ingests execution triggers from two sources: automated cron schedules (Amazon EventBridge) and on-demand manual triggers (Amazon API Gateway). Both sources are normalized into a single Amazon SQS queue, backed by a Dead Letter Queue (DLQ) to capture failed execution requests. An AWS Lambda function (Coordinator) acts as the queue consumer, calling the `RunTask` API to launch a short-lived Amazon ECS Fargate task that runs the Playwright test suite packaged within a Docker image stored in Amazon ECR. Upon completion, the task uploads HTML/JSON reports to a private Amazon S3 bucket, streams real-time execution logs to Amazon CloudWatch, and automatically shuts down.

A Post-processing AWS Lambda function parses raw execution logs, filters out noise, and passes the clean log to an external AI API to generate a concise natural language summary. This step includes a fallback mechanism to ensure the original report is still emailed if the AI invocation fails or times out. Amazon SES handles final email notifications, delivering Pass/Fail metrics, time-limited S3 Presigned URLs, and AI-generated summaries directly to registered application subscribers.

**Benefits and ROI**  
* **Elimination of manual testing:** Removes the need to manually initiate or monitor test executions.
* **Accelerated feedback loops:** Transforms a manual process that could take hours into an automated pipeline completing in minutes.
* **Cost optimization:** Pay-as-you-go model replaces maintaining round-the-clock server infrastructure.
* **Comprehensive historical auditing:** Every test run is permanently logged in Amazon DynamoDB for trend analysis—a feat nearly impossible with disparate Excel spreadsheets.
* **Empowered QA teams:** Frees QA engineers to focus on designing better test scenarios rather than executing repetitive manual checks.

### 3. Solution Architecture  
The platform is segregated into a Backend Engine (scheduling, execution, and report generation) and a Dashboard Console (management interface for Admin, QA/Tester, and Developer roles). All execution requests strictly follow a single unified pipeline: `SQS -> Lambda Coordinator -> Fargate`, with no shortcuts bypassing this chain whether triggered manually or via schedule.

![System Architecture Diagram](/images/2-Proposal/architecture.png)

**AWS Services Utilized**

| AWS Service | Architectural Role |
|---|---|
| Amazon EventBridge | Schedules and emits recurring test events using cron expressions. |
| Amazon API Gateway (HTTP API) | Ingests manual execution requests and Dashboard API calls, authenticated via Lambda Authorizer. |
| Amazon SQS + DLQ | Buffers and normalizes all test execution requests; DLQ stores repeated execution failures. |
| AWS Lambda (Coordinator) | Consumes queue messages and triggers ECS `RunTask` API calls to launch Fargate tasks. |
| Amazon ECS Fargate | Executes Docker/Playwright test containers inside a Private Subnet and auto-terminates upon completion. |
| Amazon ECR | Stores containerized Docker images containing the Playwright test runner. |
| Amazon S3 (2 Buckets) | One bucket hosts the static Dashboard frontend; one private bucket stores test reports and artifacts. |
| Amazon CloudWatch | Collects logs, metrics, and triggers alerts for task timeouts or DLQ message backlog. |
| AWS Lambda (Post-processing) | Cleans logs, calls the external AI API, and formats final notification payloads. |
| Google Gemini (External to AWS) | Generates natural language summaries from raw execution logs (replaces Amazon Bedrock due to Free Tier limits). |
| NAT Gateway (Public Subnet) | Enables the Post-processing Lambda within the Private Subnet to reach external AI API endpoints. |
| Amazon SES | Delivers email notifications directly to registered recipient lists. |
| Amazon DynamoDB | Persists test execution history, Audit Logs, and system configuration data. |
| Amazon CloudFront | Distributes the static Dashboard frontend; integrated with CLOUDFRONT-scoped AWS WAF. |
| Amazon Cognito | Authenticates Dashboard users and issues JSON Web Tokens (JWT) for Role-Based Access Control (RBAC). |
| AWS Secrets Manager | Securely stores AI API keys and sensitive system credentials. |
| Amazon VPC + VPC Endpoints (PrivateLink) | Isolates Fargate tasks in Private Subnets; routes internal AWS service calls without touching the public Internet. |
| AWS WAF (CloudFront + Regional Scopes) | Implements two Web ACLs protecting CloudFront and API Gateway endpoints respectively. |

**Component Design**
* **Trigger Layer:** EventBridge and API Gateway push both scheduled and manual requests into a shared SQS queue.
* **Execution Layer:** Lambda Coordinator launches an isolated ECS Fargate task per test run. Tasks pull Playwright images from ECR and execute headless within Private Subnets.
* **Reporting Layer:** HTML/JSON reports are saved to a private S3 bucket, while logs stream to CloudWatch in real time.
* **AI Layer:** A second Lambda cleans raw logs and invokes the external AI API via NAT Gateway, utilizing a circuit-breaker pattern to fallback to original reports if AI calls fail.
* **Notification Layer:** SES dispatches emails with Pass/Fail metrics and time-limited S3 Presigned URLs to registered application subscribers.
* **Access Layer:** Cognito consistently enforces RBAC boundaries (Admin, QA/Tester, Developer) at the API Gateway perimeter.

### 4. Technical Implementation  
**Implementation Phases**  
1. *Research & Architecture Design*: Define serverless components and network isolation patterns.
2. *Cost Estimation & Feasibility Analysis*: Model operational costs against expected execution volume.
3. *Architecture Refinement*: Optimize component choices for performance and budget constraints.
4. *Development, Testing, & Deployment*:
   * **Environment & Container Setup:** Configure IAM/AWS CLI; construct Docker/Playwright runner (`Dockerfile`, `entrypoint.js`, `playwright.config.js`).
   * **Event Flow & Coordinator:** Provision SQS + DLQ pipelines and deploy Lambda Coordinator to invoke ECS `RunTask`.
   * **Storage & Monitoring:** Configure S3 buckets (frontend + reports), CloudWatch log groups/alarms, and DynamoDB history tables.
   * **AI Summarization:** Develop Post-processing Lambda integrated with Secrets Manager for Google Gemini API keys, utilizing circuit-breaker fallbacks.
   * **Dashboard & Authentication:** Deploy static hosting via CloudFront + S3 and set up Cognito User Pools.
   * **Security Hardening:** Enforce least-privilege IAM policies and configure VPC Endpoints.
   * **Integration Testing & Demo:** Execute end-to-end pipeline validation tests.

**Technical Prerequisites**  
* **Test Runner:** Node.js, Playwright, multi-stage Docker builds to generate ECR images.
* **Coordinator Logic:** AWS SDK calls executing ECS `RunTask` triggered by SQS events.
* **Infrastructure as Code (IaC):** Provision resources via IaC (e.g., AWS CDK / CloudFormation) to ensure environmental reproducibility.
* **Security Baseline:** Fine-grained IAM roles (avoiding `*FullAccess` wildcards), Secrets Manager for credentials, and VPC Endpoints for private AWS service communication.

### 5. Roadmap & Key Milestones  
**Phase 1: AWS Foundational Self-Study (Weeks 1 – 8)**  
Individual self-study period for team members. Although individual paths varied slightly, all members achieved the required technical foundation to execute the workshop topic:
* **AWS Fundamentals & Security:** IAM (Users, Roles, Policies), VPC & Basic Networking, AWS Secrets Manager.
* **Compute & Containerization:** Docker, Amazon ECS/Fargate, Amazon ECR.
* **Serverless & Event Integration:** AWS Lambda, Amazon API Gateway, Amazon EventBridge, Amazon SQS.
* **Storage & Data Services:** Amazon S3, Amazon DynamoDB, Amazon CloudFront.
* **Monitoring & User Authentication:** Amazon CloudWatch, Amazon Cognito.

### Phase 2: Implementation
| Week | Deliverables & Progress |
| :--- | :--- |
| **Week 9** | - Convened team meetings to propose the automated testing project utilizing Playwright, evaluating feasibility, and defining appropriate development strategies.<br>- Finalized the project topic, outlined work distribution plans, and drafted the initial architecture diagram. |
| **Week 10** | - Conducted joint research and referenced various architectural designs to identify and correct flaws in the initial diagram.<br>- Submitted the first architecture draft to mentors/advisors for review; upon receiving feedback on structural shortcomings, re-evaluated the design and transitioned to a layered architecture approach for enhanced clarity. |
| **Week 11** | - Refined and finalized the architecture diagram, resolving identified design errors.<br>- Submitted the updated diagram for a second review and received approval.<br>- Formulated execution steps and assigned specific task modules to each team member; members prepared necessary configuration files and codebases for the upcoming deployment phase. |
| **Week 12** | - Initiated the hands-on implementation phase based on individual module assignments.<br>- Conducted initial integration runs, performed joint debugging, and tested all system workflows to resolve logic errors.<br>- Finalized the functional workshop demo and commenced technical report writing. |

### 6. Budget Estimation  
The table below details cost estimates generated via the AWS Pricing Calculator (Asia Pacific - Singapore region), assuming ~50 Fargate tasks/day (avg. duration 1 min/task), ~10,000 Lambda invocations/month, 5 Dashboard users (Cognito MAU), and ~4,500 SES emails sent/month:

| AWS Service | Primary Configuration Details (AWS Calculator) | Monthly Cost (USD) |
|---|---|---:|
| AWS Fargate | Linux/x86, 50 tasks/day, 1 min/task, 2 GB RAM, 20 GB Ephemeral Storage | $1.88 |
| AWS Lambda | 10,000 requests/month, 512 MB Ephemeral Storage | $0.00 |
| Amazon SQS | 0.0045 Million Standard Requests/month | $0.00 |
| Amazon S3 – Frontend | 1 GB Storage, 50 PUTs, 1,500 GETs/month | $0.03 |
| Amazon S3 – Reports | 3 GB Storage, 37,500 PUTs, 200 GETs/month | $0.26 |
| Amazon CloudWatch | 2.2 GB Log Ingested, 1 Dashboard, 3 Standard Alarms, 100 API requests | $1.85 |
| Amazon DynamoDB (On-Demand) | 1 GB Storage, average item size 5 KB | $1.88 |
| Amazon VPC – PrivateLink | 3 VPC Interface Endpoints/Region | $0.05 |
| AWS Secrets Manager | 1 Secret stored 30 days, 1,500 API calls/month | $0.41 |
| Amazon Cognito | 5 MAU with Advanced Security Features enabled | $0.26 |
| Amazon CloudFront | 2,000 HTTPS Requests, 1 GB Data Out from Origin, 1 GB Data Out to Internet | $0.11 |
| Amazon API Gateway (HTTP API) | 0.0075 Million requests/month, 34 KB/request | $0.01 |
| Amazon SES | 4,500 emails sent via Lambda/month | $0.45 |
| **Cumulative Subtotal (Calculator)** | — | **$7.19** |
| NAT Gateway *(Scheduled Creation/Deletion)* | Operates only during scheduled execution windows rather than 24/7 | **$15.045** |
|Google Gemini AI API| free tier | 0.00 | 
| **Total Estimated Cost** | — | **$22.24** |

At **$22.24 USD/month**, total annual AWS infrastructure costs are estimated at approximately **$266.82 USD**.

*Important Engineering Note:* NAT Gateway does not support native Start/Stop operations like EC2 instances. To achieve the optimized cost of $15.045 USD/month (compared to ~$43 USD/month for 24/7 continuous operation), an EventBridge Schedule triggers a Lambda function to dynamically create (`CreateNatGateway`) the gateway prior to test execution windows and delete it (`DeleteNatGateway`) upon job completion. The operational trade-off is a 1-to-3 minute provisioning delay while NAT Gateway transitions to an active state, which may introduce latency for off-schedule manual test runs.

### 7. Risk Assessment  
**Risk Matrix**  

| Risk Factor | Impact Level | Probability |
|---|---|---|
| Fargate tasks exceed timeout limits or freeze unexpectedly | Medium | Medium |
| External AI API unavailable or rate-limited | Low *(Mitigated by Fallback)* | Medium |
| IAM roles assigned overly permissive rights during development | **High** | Medium |
| Messages accumulating silently in SQS DLQ without alerts | Medium | Low |
| Unexpected AWS cost spikes due to NAT Gateway misconfiguration or long CloudWatch log retention | Medium | Low |
| Latency when QA triggers manual tests outside scheduled NAT Gateway operational windows | Medium | Medium |
| Target demo website experiences instability (if testing third-party sites) | Medium | Medium |

**Mitigation Strategies**  
* Configure CloudWatch Alarms for ECS task execution duration and DLQ queue depth to identify hung or repeating failed tasks early.
* Enclose external AI API calls behind a circuit-breaker pattern to ensure base Pass/Fail reports send successfully via email regardless of AI availability.
* Enforce strict least-privilege IAM policies from initial deployment rather than deferring cleanup to post-production.
* Implement automated `DLQ -> CloudWatch Alarm -> SNS` notification paths to ensure no job failures go unnoticed.
* Host a dedicated internal demo website rather than relying on third-party live domains to avoid anti-bot blockers or unexpected UI shifts.
* For off-schedule manual test triggers, perform NAT readiness checks prior to AI API invocation or accept the 1–3 minute startup window (aligned across team workflows).

**Contingency Plans**  
* If the external AI provider fails to respond, the system bypasses summarization and sends standard Pass/Fail email reports.
* If a Fargate task hangs, CloudWatch alarms trigger automated task termination without impacting concurrent executions (tasks remain completely isolated).
* If AWS expenditure trends upward abnormally, AWS Budget Alerts trigger notifications early enough to adjust log retention policies or NAT operational schedules.

### 8. Expected Outcomes  
**Technical Enhancements**: 
* Replaces manual E2E testing with an automated, event-driven pipeline, eliminating persistent idle server infrastructure.
* Every test execution—whether Pass or Fail—is permanently archived in DynamoDB for historical trend analysis.
* Fine-grained Role-Based Access Control (Admin / QA-Tester / Developer) is enforced consistently at the API boundary.

**Long-Term Value**:
* Establishes a re-usable reference architecture for future serverless and event-driven projects across the organization.
* Accumulated test history in DynamoDB serves as a foundational dataset for identifying recurring application regressions over time.
* Demonstrates a clear, usage-based cost model that offers significant savings over traditional, continuously running test servers.