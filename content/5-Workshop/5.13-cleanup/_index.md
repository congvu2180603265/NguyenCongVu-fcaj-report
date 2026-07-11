---
title : "Clean up"
date : 2026-07-10
weight : 13
chapter : false
pre : " <b> 5.13. </b> "
---

#### Remove All Resources Created in This Workshop

To avoid unnecessary AWS charges after completing the workshop, delete all resources in the reverse order of creation.

{{% notice warning %}}
Deleting resources is irreversible. Double-check that you no longer need any data (S3 reports, DynamoDB records, CloudWatch logs) before proceeding.
{{% /notice %}}

{{% notice note %}}
Content for this section will be updated with the full step-by-step cleanup guide.
{{% /notice %}}

**Resources to delete (in order):**

1. **CloudFront** — disable and delete the distribution
2. **Amazon Cognito** — delete the User Pool `playwright-user-pool`
3. **Amazon API Gateway** — delete the API `playwright-api`
4. **AWS Lambda** — delete all 4 Lambda functions
5. **Amazon EventBridge** — delete the rule
6. **Amazon ECS** — delete the Task Definition and Cluster
7. **Amazon ECR** — delete the `playwright-runner` repository
8. **Amazon SQS** — delete the queues `playwright-task-queue` and `playwright-dlq`
9. **AWS Secrets Manager** — delete the secret
10. **Amazon SES** — remove verified identities (optional)
11. **Amazon DynamoDB** — delete all 4 tables
12. **Amazon S3** — empty and delete `playwright-webui-xxx` and `playwright-report-2026`
13. **Amazon VPC** — delete NAT Gateway, release Elastic IP, then delete Subnets, Internet Gateway, Security Groups, Route Tables, and VPC
14. **IAM** — delete the 4 IAM roles created in section 5.2.3
