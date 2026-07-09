---
title: "Week 10 Worklog"
date: 2026-06-19
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### Week 10 Objective:
This week I focused on turning the team project idea into a clearer architecture, especially the post-test processing flow. Instead of stopping at automated test execution, I started designing how test logs would be collected, summarized by AI, stored as reports, and shared back to users.

### Work completed during the week:
| Day | Date | Task | Reference Material |
|---|---|---|---|
| Friday | 06/19/2026 | Reviewed the project direction and drafted several architecture flows, separating test execution, log collection, report generation, and result delivery | Team notes |
| Saturday | 06/20/2026 | Worked with the team on the detailed project description; I focused on describing the Post-processing & AI block that runs after a test task finishes | Team notes |
| Sunday | 06/21/2026 | Refined the data design around reporting needs: test history, error details, AI summary content, and report access links | docs.aws.amazon.com/dynamodb |
| Monday | 06/22/2026 | Clarified the role of the post-processing Lambda function: read CloudWatch logs, collect relevant failure information, call an External AI API, and prepare report data | docs.aws.amazon.com/lambda |
| Tuesday | 06/23/2026 | Added the report storage flow with S3 HTML/JSON outputs and studied Presigned URLs so users can access reports without making the whole bucket public | docs.aws.amazon.com/s3 |
| Wednesday | 06/24/2026 | Presented the architecture with the team for mentor review; noted feedback about data flow, permissions, and boundaries between Lambda, S3, CloudWatch, DynamoDB, and the AI API | Review notes |
| Thursday | 06/25/2026 | Studied VPC Endpoints from the deployment perspective: Gateway Endpoints for S3/DynamoDB and Interface Endpoints for ECR; also documented how Secrets Manager should protect the AI API key | docs.aws.amazon.com/vpc |

### Week 10 Achievements:
* Defined my project responsibility more clearly: the Post-processing & AI flow for automated test results.
* Drafted the Lambda workflow for reading CloudWatch logs, calling an External AI API, and preparing summarized error reports.
* Designed the approach for storing HTML/JSON reports in S3 and sharing them through Presigned URLs.
* Added Secrets Manager to the architecture to keep the AI API key outside the source code and unsafe configuration.
* Improved my understanding of how VPC Endpoints support private access to S3, DynamoDB, and ECR.
* Contributed to the detailed system description and architecture diagram that includes CloudFront, S3, Cognito, API Gateway, SQS, ECS Fargate, ECR, DynamoDB, Lambda, CloudWatch, Secrets Manager, VPC, and SES.
