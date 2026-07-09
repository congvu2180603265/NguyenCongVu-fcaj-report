---
title: "Week 12 Worklog"
date: 2026-07-03
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
---

### Week 12 Objective:
Week 12 moved from design to deployment. I focused on setting up the resources for the Post-processing & AI block: DynamoDB tables, the report bucket, VPC Endpoints, secret configuration for the AI API key, SES testing, and team coordination for an end-to-end flow from test execution to final report.

### Work completed during the week:
| Day | Date | Task | Reference Material |
|---|---|---|---|
| Friday | 07/03/2026 | Broke down the deployment plan with the team and reviewed VPC, subnets, route tables, NAT Gateway, and security groups to make sure the post-processing Lambda had both private AWS access and external API access | Deployment notes |
| Saturday | 07/04/2026 | Prepared the post-processing configuration: report bucket name, HTML/JSON folder structure, Lambda environment variables, secret name for the AI API key, and required IAM permissions | AWS IAM docs |
| Sunday | 07/05/2026 | Created the two DynamoDB tables for test history and error logs; coordinated the creation of the S3 report bucket, SQS task queue, and Dead Letter Queue for the processing workflow | AWS Console, Boto3 |
| Monday | 07/06/2026 | Created a Gateway VPC Endpoint for S3 and an Interface VPC Endpoint for ECR; verified an SES email, sent a test email, and supported Lambda/EventBridge Scheduler setup for the automated flow | docs.aws.amazon.com/vpc, docs.aws.amazon.com/ses |
| Tuesday | 07/07/2026 | Tested post-test data handling: writing run history to DynamoDB, saving error records, checking the AI summary field, and updating processing status step by step | Testing notes |
| Wednesday | 07/08/2026 | Ran an end-to-end trial with the team; checked whether Lambda could receive logs, generate HTML/JSON reports, store them in S3, and prepare Presigned URLs for user access | Debug notes |
| Thursday | 07/09/2026 | Summarized my personal contribution for the internship report and documented issues found while connecting Lambda, DynamoDB, S3, and SES, including IAM and environment variable adjustments | Internship report |

### Week 12 Achievements:
* Deployed the main resources for the project's Post-processing & AI block.
* Created and configured two DynamoDB tables for test history and error log storage.
* Prepared the S3 report bucket for HTML/JSON outputs and defined the Presigned URL sharing approach.
* Configured VPC Endpoints for S3/ECR to support safer communication from private subnets to AWS resources.
* Verified an SES email and sent a successful test message for the report notification feature.
* Completed initial testing of the post-processing flow across logs, DynamoDB data, S3 reports, and result notification.
