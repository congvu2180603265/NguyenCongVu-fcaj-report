---
title : "Dọn dẹp tài nguyên"
date : 2026-07-10
weight : 13
chapter : false
pre : " <b> 5.13. </b> "
---

#### Xóa tất cả tài nguyên đã tạo trong workshop

Để tránh phát sinh chi phí AWS không cần thiết sau khi hoàn thành workshop, hãy xóa toàn bộ tài nguyên theo thứ tự ngược lại so với lúc tạo.

{{% notice warning %}}
Việc xóa tài nguyên là không thể hoàn tác. Hãy kiểm tra kỹ rằng bạn không còn cần dữ liệu nào (báo cáo S3, bản ghi DynamoDB, log CloudWatch) trước khi tiến hành.
{{% /notice %}}

{{% notice note %}}
Nội dung phần này sẽ được cập nhật với hướng dẫn dọn dẹp chi tiết từng bước.
{{% /notice %}}

**Tài nguyên cần xóa (theo thứ tự):**

1. **CloudFront** — vô hiệu hóa và xóa distribution
2. **Amazon Cognito** — xóa User Pool `playwright-user-pool`
3. **Amazon API Gateway** — xóa API `playwright-api`
4. **AWS Lambda** — xóa cả 4 Lambda function
5. **Amazon EventBridge** — xóa rule
6. **Amazon ECS** — xóa Task Definition và Cluster
7. **Amazon ECR** — xóa repository `playwright-runner`
8. **Amazon SQS** — xóa các queue `playwright-task-queue` và `playwright-dlq`
9. **AWS Secrets Manager** — xóa secret
10. **Amazon SES** — hủy xác minh danh tính (tùy chọn)
11. **Amazon DynamoDB** — xóa cả 4 bảng
12. **Amazon S3** — xóa toàn bộ nội dung rồi xóa bucket `playwright-webui-xxx` và `playwright-report-2026`
13. **Amazon VPC** — xóa NAT Gateway, giải phóng Elastic IP, sau đó xóa Subnet, Internet Gateway, Security Group, Route Table và VPC
14. **IAM** — xóa 4 IAM role đã tạo ở phần 5.2.3
