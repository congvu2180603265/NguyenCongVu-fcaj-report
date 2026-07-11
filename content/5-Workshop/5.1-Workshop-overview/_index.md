---
title : "Introduction"
date : 2026-07-10
weight : 1 
chapter : false
pre : " <b> 5.1. </b> "
---

#### Introduction to AWS Cloud-Native & Serverless Automation

Event-driven Cloud-Native Architecture is the optimal model for modern automated testing systems. By leveraging Serverless services on AWS, the system only provisions resources on demand and automatically releases them after each task completes — optimizing costs, improving scalability, and ensuring high availability.

The testing environment is containerized with Docker and Playwright to ensure consistency across executions. Containers run on Amazon ECS Fargate inside a Private Subnet to perform End-to-End tests, store reports on Amazon S3, write logs to Amazon CloudWatch, and update status in Amazon DynamoDB. The system also integrates Google Gemini (External AI API) to automatically analyze logs, summarize test results, and suggest error remediation before delivering reports to users.

#### Workshop Overview

In this workshop, you will build and deploy a Cloud-Native End-to-End (E2E) automated testing platform on AWS. The system follows a multi-layer architecture designed for scalability, flexibility, and maintainability.

- **"Backend Engine"** receives test requests from Amazon API Gateway or Amazon EventBridge, then routes them into Amazon SQS for asynchronous processing. AWS Lambda orchestrates the workflow by launching Docker containers running Playwright on Amazon ECS Fargate. After completion, reports are stored on Amazon S3, logs are recorded in Amazon CloudWatch, and resources are automatically released under the Pay-as-you-go model to optimize operational costs.

- **"Dashboard Console"** is deployed on Amazon S3 combined with Amazon CloudFront, providing an admin interface for Admins, QA/Testers, and Developers to manage test scenarios, track execution history, and view test reports. Users are authenticated via Amazon Cognito, while Amazon API Gateway and AWS Lambda handle requests from the frontend. All test data and status are stored in Amazon DynamoDB.

- **"AI Support & Notification"** uses AWS Lambda to process and clean log data before forwarding it to Google Gemini (External AI API) for analysis, result summarization, and root-cause suggestions. Once complete, the system automatically sends email reports via Amazon SES with S3 report download links — and ensures delivery even when the AI service is temporarily unavailable.

![Architecture](/images/5-Workshop/5.1-Workshop-overview/Architecture.png?featherlight=false&width=90pc)

---

Next, we will move on to **[5.2. Prerequisites](../5.2-Prerequisite/)** to set up the necessary tools and infrastructure.
