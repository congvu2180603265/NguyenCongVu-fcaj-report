---
title: "Week 11 Worklog"
date: 2026-06-26
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### Week 11 Objective:
Week 11 was the final architecture planning stage before deployment. My main focus was defining the Post-processing & AI block for the team project: a Lambda function that processes test logs, calls an External AI API to summarize failures, stores reports in S3, generates Presigned URLs, and uses Secrets Manager to protect the API key.

### Work completed during the week:
| Day | Date | Task | Reference Material |
|---|---|---|---|
| Friday | 06/26/2026 | Reviewed the overall architecture with the team from a cost-optimization perspective; we removed SNS from the current flow and replaced AWS Bedrock with an External AI API because of practice account limitations | Team meeting notes |
| Saturday | 06/27/2026 | Reworked my post-test processing flow: CloudWatch stores execution logs, the post-processing Lambda reads those logs, extracts failure details, and prepares the payload for the AI API | docs.aws.amazon.com/cloudwatch, docs.aws.amazon.com/lambda |
| Sunday | 06/28/2026 | Refined the data that needs to be stored after processing, including test status, main error details, AI summary content, HTML/JSON report links, and report creation time | docs.aws.amazon.com/dynamodb |
| Monday | 06/29/2026 | Studied S3 report storage and Presigned URLs so users can open generated reports without making the bucket public; noted how to separate HTML and JSON outputs for each test run | docs.aws.amazon.com/s3 |
| Tuesday | 06/30/2026 | Finalized the position of the post-processing Lambda inside private subnets; the Lambda uses NAT Gateway for the External AI API and VPC Endpoints for S3/DynamoDB access | docs.aws.amazon.com/vpc |
| Wednesday | 07/01/2026 | Received the official task assignment; confirmed my scope: Post-processing & AI, DynamoDB for history/error logs, S3 reports, Secrets Manager, VPC Endpoints, and SES reporting support | Assignment notes |
| Thursday | 07/02/2026 | Prepared a deployment checklist covering DynamoDB table names, the report bucket, the API-key secret, and Lambda IAM permissions for CloudWatch logs, S3, DynamoDB, and Secrets Manager | Boto3 documentation, AWS IAM docs |

### Week 11 Achievements:
* Clearly defined my responsibility in the team project as the Post-processing & AI block.
* Designed the Lambda flow for reading CloudWatch logs, calling an External AI API, and preparing post-test report data.
* Changed the AI approach from Bedrock to an External AI API to match the lab account constraints.
* Added S3 Report and Presigned URL handling to store and share HTML/JSON reports more safely.
* Included Secrets Manager in the design to manage the API key outside the source code.
* Understood how private subnets, NAT Gateway, and VPC Endpoints work together when a Lambda function needs both external API access and private AWS service access.
