---
title : "Cấu hình IAM"
date : 2024-01-01 
weight : 3
chapter : false
pre : " <b> 5.2.3. </b> "
---
---
title: "5.2.3 Configure IAM Roles"
weight: 23
---

# Configure IAM Roles

Trong bước này, chúng ta sẽ tạo các IAM Role cần thiết để các dịch vụ trong hệ thống có thể tương tác với nhau một cách an toàn mà không cần sử dụng Access Key.

Hệ thống sử dụng ba IAM Role chính:

- **playwright-lambda-role**: Cho phép các hàm Lambda truy cập các dịch vụ AWS như ECS, DynamoDB, SQS, S3, SES, CloudWatch Logs và Secrets Manager.
- **playwright-ecs-execution-role**: Cho phép ECS Fargate tải Docker Image từ Amazon ECR và ghi log lên CloudWatch.
- **playwright-ecs-task-role**: Cho phép container Playwright truy cập Amazon S3 và CloudWatch Logs trong quá trình thực thi.

---

# 1. Tạo Lambda Role

Truy cập:

```
AWS Console
→ IAM
→ Roles
→ Create role
```

Chọn:

- Trusted entity type: **AWS Service**
- Service: **Lambda**

Đặt tên Role:

```
playwright-lambda-role
```

![](create-lambda-role.png)

---

## Gán quyền cho Lambda Role

Gán các AWS Managed Policies sau:

- AmazonDynamoDBFullAccess
- AmazonECS_FullAccess
- AmazonS3FullAccess
- AmazonSESFullAccess
- AmazonSQSFullAccess
- CloudWatchLogsFullAccess
- IAMFullAccess
- SecretsManagerReadWrite

![](lambda-role-policies.png)

Sau khi hoàn tất, chọn **Create role**.

![](lambda-role-created.png)

---

# 2. Tạo ECS Execution Role

Tiếp tục chọn:

```
Create role
```

Chọn:

- AWS Service
- Elastic Container Service
- Elastic Container Service Task

Đặt tên:

```
playwright-ecs-execution-role
```

![](create-ecs-execution-role.png)

Role này sử dụng policy mặc định:

- AmazonECSTaskExecutionRolePolicy

Sau đó chọn **Create role**.

![](ecs-execution-role-created.png)

---

# 3. Tạo ECS Task Role

Tiếp tục tạo thêm một IAM Role mới.

Chọn:

- AWS Service
- Elastic Container Service
- Elastic Container Service Task

Đặt tên:

```
playwright-ecs-task-role
```

![](create-ecs-task-role.png)

---

## Gán quyền cho ECS Task

Role này sẽ được container Playwright sử dụng trong quá trình chạy.

Gán các quyền:

- AmazonS3FullAccess
- CloudWatchLogsFullAccess

![](ecs-task-role-policies.png)

Sau khi hoàn tất, chọn **Create role**.

![](ecs-task-role-created.png)

---

# 4. Kiểm tra kết quả

Sau khi hoàn thành, trong danh sách IAM Roles sẽ xuất hiện ba Role vừa tạo:

- playwright-lambda-role
- playwright-ecs-execution-role
- playwright-ecs-task-role

![](iam-roles-overview.png)

---

## Kết quả

Hoàn thành bước này, hệ thống đã có đầy đủ IAM Role phục vụ cho:

| IAM Role | Chức năng |
|----------|-----------|
| playwright-lambda-role | Cho phép Lambda truy cập các dịch vụ AWS |
| playwright-ecs-execution-role | Cho phép ECS tải image từ Amazon ECR và ghi CloudWatch Logs |
| playwright-ecs-task-role | Cho phép container Playwright truy cập S3 và CloudWatch Logs |

Các IAM Role này sẽ được sử dụng ở các bước tiếp theo khi cấu hình ECS Task Definition và Lambda Functions.