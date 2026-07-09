---
title: "Overview"
date: 2026-06-16
weight: 1
chapter: false
pre: " <b> 2. </b> "
---

# Automated Playwright Testing System Using Docker on AWS

## 1. Project Summary
The system is an End-to-End (E2E) automated testing platform for websites, built to completely eliminate the need for engineers to manually watch over and run tests every time a deployment change happens. Playwright runs inside a Docker container to simulate real user behavior in the browser (navigation, form input, checking displayed results), after which an AI summarization step turns raw technical logs into easy-to-understand content sent via email.

The entire system runs on AWS using an event-driven, serverless-first architecture: Amazon EventBridge triggers test runs on a schedule, Amazon SQS decouples request intake from execution, an AWS Lambda function (the Coordinator) spins up an independent Amazon ECS Fargate task for each test run, and that task automatically shuts down once the report is generated. As a result, the system only incurs cost for the minutes actually spent running tests, instead of paying for a server running 24/7 that sits idle most of the time. Access to the admin dashboard is role-based across 3 roles (Admin, QA/Tester, Developer) via Amazon Cognito.

## 2. Problem Statement

**Current problem**
Manual End-to-End testing requires a tester to manually run test scripts every time a change is deployed — this approach doesn't scale as the number of applications and test cases keeps growing. There's no centralized system to schedule tests, track Pass/Fail trends over time, or automatically notify the right stakeholders. Keeping a server running continuously just to be ready for the next test run wastes operating cost, since the server sits idle most of the time.

**Solution**
The system accepts two trigger sources — an automated cron schedule (Amazon EventBridge) and a manual on-demand trigger (Amazon API Gateway) — and normalizes both into a single Amazon SQS queue along with a Dead Letter Queue (DLQ) to retain requests that fail processing. An AWS Lambda function (Coordinator) acts as the queue's consumer, calling the RunTask API to spin up a short-lived Amazon ECS Fargate task that runs the Playwright test suite packaged as a Docker image stored in Amazon ECR. Once finished, the task writes an HTML/JSON report to a private Amazon S3 bucket and streams real-time logs to Amazon CloudWatch, then shuts itself down.

A post-processing AWS Lambda function reads the raw logs, filters out unnecessary data, and sends it to an external AI API to generate a concise natural-language summary — with a fallback mechanism that still sends the original report via email if the AI call fails or times out. Amazon SES handles sending the final notification email, which includes the Pass/Fail metrics, a time-limited S3 presigned URL, and the AI-generated summary, to the correct list of subscribers registered for that application.

**Benefits and ROI**
* Eliminates manual test execution: no more manually triggering tests or watching for results.
* Shortens response time: turns a manual process that could take hours into a single automated run lasting just minutes.
* Optimizes infrastructure cost: cost scales with actual usage (pay-as-you-go) instead of maintaining a 24/7 server.
* Full history retention: every run is permanently stored in Amazon DynamoDB, enabling quality-trend analysis that's nearly impossible with scattered Excel-based tracking.
* Frees up resources: frees up QA time to focus on designing better test scenarios instead of repeating manual checks.

## 3. Solution Architecture
The system is divided into a Backend Engine (scheduling, executing, and generating test reports) and a Dashboard Console (interface for the 3 roles: Admin, QA/Tester, Developer). Every request goes through exactly one processing flow — no shortcut ever skips the SQS → Lambda Coordinator → Fargate chain, whether triggered on schedule or manually.

![Playwright automated testing solution architecture](/images/anhProposal/png)

**AWS services used**

| AWS Service | Role in the architecture |
|---|---|
| Amazon EventBridge | Schedules and generates periodic test events (cron expressions). |
| Amazon API Gateway (HTTP API) | Accepts manual trigger requests and API calls from the Dashboard, authenticated via a Lambda Authorizer. |
| Amazon SQS + DLQ | Buffers and normalizes every test request; the DLQ retains repeatedly failing runs. |
| AWS Lambda (Coordinator) | Consumes the queue and calls ECS RunTask to spin up a Fargate task. |
| Amazon ECS Fargate | Runs the Docker/Playwright container in a Private Subnet, shutting itself down when done. |
| Amazon ECR | Stores the Docker image containing the Playwright test runner. |
| Amazon S3 (2 buckets) | One bucket serves the static Dashboard frontend; one private bucket stores reports and test artifacts. |
| Amazon CloudWatch | Collects logs and metrics, alerting when a task runs too long or the DLQ backs up. |
| AWS Lambda (Post-processing) | Cleans up logs, calls the external AI API, prepares notification content. |
| OpenAI API (outside AWS) | Generates a natural-language log summary (used instead of Amazon Bedrock due to Free Tier limitations). |
| NAT Gateway (Public Subnet) | Lets the Post-processing Lambda in the Private Subnet call the external AI API. |
| Amazon SES | Sends the final result email directly to registered recipients. |
| Amazon DynamoDB | Stores test history, audit logs, and system configuration data. |
| Amazon CloudFront | Distributes the static Dashboard frontend, paired with WAF at the CLOUDFRONT scope. |
| Amazon Cognito | Authenticates Dashboard users and issues tokens for role-based access. |
| AWS Secrets Manager | Stores the AI API key and other sensitive configuration. |
| Amazon VPC + VPC Endpoints (PrivateLink) | Isolates Fargate in a Private Subnet; internal AWS calls never traverse the public internet. |
| AWS WAF (CloudFront scope + Regional scope) | Two separate Web ACLs protecting CloudFront and the API Gateway endpoint. |

**Component design**
* Trigger layer: Both EventBridge and API Gateway push events into the same SQS queue.
* Execution layer: The Coordinator Lambda spins up an ECS Fargate task for each run; the task pulls the Playwright image from ECR and runs headless inside a Private Subnet.
* Reporting layer: HTML/JSON reports are stored in a private S3 bucket; logs stream to CloudWatch in real time.
* AI layer: A second Lambda cleans logs and calls the AI API through a NAT Gateway, with a circuit-breaker mechanism to fall back to the original report if the AI call fails.
* Notification layer: SES sends emails to the recipients registered for that application, including a time-limited S3 presigned URL.
* Access layer: Cognito consistently enforces the 3 roles (Admin, QA/Tester, Developer) at the API Gateway boundary.

## 4. Technical Implementation

**Implementation phases**
The architecture has gone through several rounds of review and is finalized; the team is now in the actual implementation phase, broken down into the following steps:
1. Environment & Container Setup: Configure IAM/AWS CLI, build the Docker/Playwright runner (Dockerfile, entrypoint.js, playwright.config.js).
2. Event Flow & Coordinator: Set up SQS + DLQ and the Coordinator Lambda that calls ECS RunTask.
3. Storage & Monitoring: S3 (frontend + reports), CloudWatch log groups and alarms, DynamoDB table for history and audit logs.
4. AI Summarization: Post-processing Lambda integrated with Secrets Manager for the OpenAI API key and a circuit-breaker style fallback mechanism.
5. Dashboard & Access Control: Static hosting via CloudFront + S3, Cognito user pool, Lambda Authorizer, and the 3-role interface (Admin / QA-Tester / Developer).
6. Security Hardening: Least-privilege IAM policies, VPC Endpoints, and two WAF scopes (CloudFront and Regional).
7. Integration Testing & Demo: Run an end-to-end test on a self-built demo website (avoiding third-party anti-bot policy issues), validate the full pipeline, and prepare the report.

**Technical requirements**
* Test runner: Node.js, Playwright, Docker (multi-stage build) to produce the image pushed to ECR.
* Coordinator logic: Uses the AWS SDK to call ECS RunTask from a Lambda function triggered by SQS.
* Infrastructure as Code (IaC): Recommend provisioning resources with IaC (e.g., AWS CDK/CloudFormation) to ensure reproducibility across environments.
* Security foundation: Scoped IAM roles per component (no *FullAccess policies), Secrets Manager for credentials, and VPC Endpoints for internal service calls.

## 5. Roadmap & Milestones

| Week | Milestone | Owner |
|---|---|---|
| | | |

## 6. Budget Estimation

| AWS Service | Key configuration entered in the Calculator | Cost/month (USD) |
|---|---|---|
| AWS Fargate | Linux/x86, 50 tasks/day, 1 min/task, 2 GB RAM, 20 GB ephemeral storage | 1.88 |
| AWS Lambda | 10,000 requests/month, 512 MB ephemeral storage | 0.00 |
| Amazon SQS | 0.0045 million standard requests/month | 0.00 |
| Amazon S3 – Frontend | 1 GB storage, 50 PUT, 1,500 GET/month | 0.03 |
| Amazon S3 – Reports | 3 GB storage, 37,500 PUT, 200 GET/month | 0.26 |
| Amazon CloudWatch | 2.2 GB log ingested, 1 dashboard, 3 standard alarms, 100 other API requests | 1.85 |
| Amazon DynamoDB | On-Demand, 1 GB storage, average item 5 KB | 1.88 |
| Amazon VPC – PrivateLink | 3 VPC Interface Endpoints/region | 0.05 |
| AWS Secrets Manager | 1 secret, stored 30 days, 1,500 API calls/month | 0.41 |
| Amazon Cognito | 5 MAU, Advanced Security Features enabled | 0.26 |
| Amazon CloudFront | 2,000 HTTPS requests, 1 GB data to origin, 1 GB to internet | 0.11 |
| Amazon API Gateway | HTTP API, 0.0075 million requests/month, 34 KB/request | 0.01 |
| Amazon SES | 4,500 emails sent from Lambda/month | 0.45 |
| **Subtotal (Calculator)** | Services on the AWS Calculator | **7.19** |
| NAT Gateway | Scheduled create/delete for cost optimization, only runs during the OpenAI API call window | 15.045 |
| **Total** | Excluding OpenAI API cost | **22.24** |

At 22.24 USD/month, the total estimated AWS cost over 12 months is about 266.82 USD (excluding OpenAI API cost, since that's a third-party service outside AWS Pricing Calculator and needs to be added manually based on actual token usage).

{{% notice warning %}}
Important technical note: NAT Gateway doesn't support Start/Stop like EC2 — to achieve the optimized cost of 15.045 USD/month (instead of about 43 USD/month if run 24/7), the scheduling approach needs to use an EventBridge Schedule combined with a Lambda function to automatically create it (CreateNatGateway) before the needed time window and delete it (DeleteNatGateway) afterward. The trade-off of this approach is that the NAT Gateway takes about 1-3 minutes to become ready after creation, which can cause delays if QA triggers a manual test outside the scheduled window — this trade-off should be clearly stated in the report's risk section.
{{% /notice %}}

## 7. Risk Assessment

**Risk matrix**

| Risk | Impact | Likelihood |
|---|---|---|
| Fargate task runs longer than expected / times out | Medium | Medium |
| External AI API (OpenAI) unavailable or rate-limited | Low (fallback in place) | Medium |
| IAM role granted excessive permissions during development | High | Medium |
| Messages piling up in the DLQ without an alert | Medium | Low |
| AWS cost exceeding expectations due to NAT misconfiguration or CloudWatch retention settings | Medium | Low |
| Latency when QA triggers an ad-hoc test outside the scheduled NAT Gateway window | Medium | Medium |
| Demo website instability (if using a real third-party site) | Medium | Medium |

**Mitigation strategy**
* Set up CloudWatch Alarms for ECS task duration and DLQ depth to catch stuck or repeatedly failing runs early.
* Keep the AI summarization step behind a circuit-breaker so the original report still gets emailed if the AI call fails.
* Apply least-privilege IAM from the start of implementation, rather than leaving it for a later cleanup phase.
* Set up the DLQ → CloudWatch Alarm → SNS alert chain early so no failed run silently goes unnoticed.
* Use a self-built demo website instead of a real third-party domain, to avoid anti-bot issues and unpredictable UI changes.
* If a test is manually triggered outside the NAT Gateway's scheduled window, the system can check the NAT's status before calling the AI API, or accept a 1-3 minute delay for the NAT to initialize.

**Contingency plan**
* If the AI provider doesn't respond, the system still sends the original Pass/Fail report via email.
* If a Fargate task hangs, the CloudWatch alarm triggers and the task can be force-stopped without affecting other runs (each run is isolated independently).
* If AWS cost trends up abnormally, a budget alert will catch it early enough to adjust log retention or NAT usage.

## 8. Expected Outcomes

**Technical improvements**
Manual E2E testing is replaced by an automated, event-driven pipeline, with no more idle standing infrastructure. Role-based access (Admin / QA-Tester / Developer) is consistently enforced at the API boundary.

**Long-term value**
Becomes a reusable reference architecture for the team's future serverless, event-driven projects. Accumulated test history data (DynamoDB) forms the foundation for analyzing recurring failures later on. Demonstrates a clear, usage-based cost model compared to a traditional always-on test server.