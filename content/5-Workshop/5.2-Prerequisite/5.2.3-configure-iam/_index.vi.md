---
title : "Cấu hình IAM"
date : 2026-06-13
weight : 3
chapter : false
pre : " <b> 5.2.3. </b> "
---

## Cấu hình IAM Role cho Lambda và ECS

Trong phần này, chúng ta sẽ tạo bốn IAM Role cho hệ thống kiểm thử Playwright:

| IAM Role | Trusted service | Mục đích |
| --- | --- | --- |
| `playwright-lambda-role` | AWS Lambda | Cho phép Lambda điều phối ECS và xử lý kết quả |
| `playwright-postprocessing-role` | AWS Lambda | Role riêng cho Lambda `playwright-postprocessing`, tách biệt để kiểm soát quyền gọi Secrets Manager và SES chặt chẽ hơn |
| `playwright-ecs-execution-role` | ECS Tasks | Cho phép ECS agent tải image từ ECR và ghi log container |
| `playwright-ecs-task-role` | ECS Tasks | Cấp quyền cho ứng dụng Playwright bên trong container |

{{% notice warning %}}
Tuân thủ nguyên tắc cấp quyền tối thiểu (least privilege). Các policy `FullAccess` xuất hiện trong ảnh chụp chỉ phù hợp cho mục đích minh họa workshop. Không cấp `IAMFullAccess` cho Lambda hoặc ECS task trong môi trường thực tế.
{{% /notice %}}

---

### 1. Tạo Lambda execution role `playwright-lambda-role`

Mở **AWS Management Console**, tìm **IAM**, sau đó chọn:

```text
Roles → Create role
```

**Step 1 – Select trusted entity:** Chọn **AWS service** làm trusted entity, trong phần **Use case** chọn **Lambda**, rồi nhấn **Next**.

![Chọn trusted entity là Lambda](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/1a-lambda-select-trusted-entity.png?featherlight=false&width=90pc)

**Step 2 – Add permissions:** Tìm kiếm và attach lần lượt các AWS managed policy sau, rồi nhấn **Next**.

| Policy | Mục đích |
| --- | --- |
| `AmazonSQSFullAccess` | Cho phép Lambda gửi/nhận message từ SQS queue |
| `AmazonECS_FullAccess` | Cho phép Lambda khởi chạy và theo dõi ECS task |
| `AmazonDynamoDBFullAccess` | Cho phép đọc/ghi bảng DynamoDB của workshop |
| `CloudWatchLogsFullAccess` | Cho phép ghi log lên CloudWatch Logs |
| `SecretsManagerReadWrite` | Cho phép đọc secret (API key, credentials) |
| `AmazonS3FullAccess` | Cho phép đọc/ghi báo cáo trong S3 bucket |
| `AmazonSESFullAccess` | Cho phép gửi email qua Amazon SES |
| `IAMFullAccess` | Cho phép Lambda truyền role cho ECS task |

![Attach policy cho playwright-lambda-role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/1b-lambda-add-permissions.png?featherlight=false&width=90pc)

**Step 3 – Name, review, and create:** Đặt tên role:

```text
playwright-lambda-role
```

![Điền tên playwright-lambda-role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/1-create-lambda-role.png?featherlight=false&width=90pc)

Kiểm tra lại phần **Permissions policies** đã có đủ 8 policy ở trên, trust policy có chứa service principal `lambda.amazonaws.com`, sau đó chọn **Create role**.

![Kiểm tra và tạo role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/2-lambda-role-created.png?featherlight=false&width=90pc)

{{% notice note %}}
Với môi trường production, hãy thay các policy `FullAccess` của AWS bằng policy tùy chỉnh giới hạn theo action và resource ARN cụ thể. Secrets Manager chỉ cần quyền `secretsmanager:GetSecretValue`. Không cấp `IAMFullAccess` — chỉ cần quyền `iam:PassRole` giới hạn theo ARN.
{{% /notice %}}

---

### 2. Tạo role riêng cho Lambda Post-processing

Ngoài `playwright-lambda-role` dùng chung ở Bước 1, Lambda `playwright-postprocessing` sử dụng **một role riêng** — vì hàm này gọi Secrets Manager để lấy AI API Key và gọi SES để gửi email nhiều hơn các Lambda khác trong hệ thống, nên tách riêng để dễ kiểm soát và thu hẹp quyền sau này.

Tạo thêm một role mới, chọn:

**Step 1 – Select trusted entity:** Chọn **AWS service** làm trusted entity, trong phần **Use case** chọn **Lambda**, rồi nhấn **Next**.

![Chọn trusted entity là Lambda](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/2a-postprocessing-select-trusted-entity.png?featherlight=false&width=90pc)

**Step 2 – Add permissions:** Attach các AWS managed policy sau, rồi nhấn **Next**.

| Policy | Mục đích |
| --- | --- |
| `AmazonDynamoDBFullAccess` | Cập nhật trạng thái kết quả kiểm thử |
| `AmazonECS_FullAccess` | Cho phép hàm khởi chạy và theo dõi ECS task |
| `AmazonS3FullAccess` | Đọc/ghi báo cáo Playwright trong S3 |
| `AmazonSESFullAccess` | Gửi email thông báo kết quả kiểm thử |
| `AmazonSQSFullAccess` | Cho phép gửi/nhận message từ SQS queue |
| `AWSLambdaVPCAccessExecutionRole` | Cho phép Lambda kết nối vào VPC |
| `CloudWatchLogsFullAccess` | Ghi log thực thi hàm |
| `SecretsManagerReadWrite` | Đọc AI API Key từ Secrets Manager |

![Attach policy cho playwright-postprocessing-role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/2b-postprocessing-add-permissions.png?featherlight=false&width=90pc)

**Step 3 – Name, review, and create:** Đặt tên role:

```text
playwright-postprocessing-role
```

![Điền tên playwright-postprocessing-role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/2c-postprocessing-name.png?featherlight=false&width=90pc)

Kiểm tra lại phần **Permissions policies** đã có đủ 8 policy ở trên, trust policy có chứa service principal `lambda.amazonaws.com`, sau đó chọn **Create role**.

![Kiểm tra và tạo role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/2d-postprocessing-created.png?featherlight=false&width=90pc)

{{% notice note %}}
Ghi lại ARN của role này (dạng `arn:aws:iam::<account-id>:role/playwright-postprocessing-role`) — sẽ dùng để gắn cho Lambda `playwright-postprocessing` ở mục 5.7, thay vì `playwright-lambda-role`.
{{% /notice %}}

---

### 3. Tạo ECS task execution role

Tạo thêm một role mới.

**Step 1 – Select trusted entity:** Chọn:

| Thuộc tính | Giá trị |
| --- | --- |
| Trusted entity type | `AWS service` |
| Service or use case | `Elastic Container Service` |
| Use case | `Elastic Container Service Task` |

Sau đó nhấn **Next**.

![Đặt ECS Tasks làm trusted service](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/3-create-ecs-execution-role.png?featherlight=false&width=90pc)

**Step 2 – Add permissions:** Tìm kiếm và attach policy sau, rồi nhấn **Next**.

| Policy | Mục đích |
| --- | --- |
| `AmazonECSTaskExecutionRolePolicy` | Cho phép ECS agent tải image từ Amazon ECR và gửi log container lên CloudWatch Logs |

Đây là AWS managed policy tiêu chuẩn dành riêng cho ECS task execution role.

![Attach policy AmazonECSTaskExecutionRolePolicy](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/3b-ecs-execution-add-permissions.png?featherlight=false&width=90pc)

**Step 3 – Name, review, and create:** Đặt tên role:

```text
playwright-ecs-execution-role
```

![Điền tên playwright-ecs-execution-role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/5-playwright-ecs-execution-role.png?featherlight=false&width=90pc)

Kiểm tra trust policy dùng service principal `ecs-tasks.amazonaws.com` và có đủ policy `AmazonECSTaskExecutionRolePolicy`, sau đó chọn **Create role**.

![Kiểm tra và tạo role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/4-ecs-execution-role-created.png?featherlight=false&width=90pc)

---

### 4. Tạo ECS task role

Tạo thêm một role mới.

**Step 1 – Select trusted entity:** Chọn:

| Thuộc tính | Giá trị |
| --- | --- |
| Trusted entity type | `AWS service` |
| Service or use case | `Elastic Container Service` |
| Use case | `Elastic Container Service Task` |

Sau đó nhấn **Next**.

![Đặt ECS Tasks làm trusted service](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/6-ecs-task-role-created.png?featherlight=false&width=90pc)

**Step 2 – Add permissions:** Ứng dụng Playwright bên trong container sử dụng task role này. Attach các policy sau, rồi nhấn **Next**.

| Policy | Mục đích |
| --- | --- |
| `AmazonS3FullAccess` | Ghi báo cáo, ảnh chụp màn hình và kết quả vào S3 bucket |
| `CloudWatchLogsFullAccess` | Ghi log ứng dụng lên CloudWatch Logs |
| `SecretsManagerReadWrite` | Đọc secret (credentials, API key) cần thiết khi chạy test |

![Attach policy cho playwright-ecs-task-role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/4b-ecs-task-add-permissions.png?featherlight=false&width=90pc)

**Step 3 – Name, review, and create:** Đặt tên role:

```text
playwright-ecs-task-role
```

![Điền tên và review playwright-ecs-task-role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/8-ecs-task-role-policies.png?featherlight=false&width=90pc)

Kiểm tra trust policy dùng service principal `ecs-tasks.amazonaws.com` và có đủ 3 policy ở trên, sau đó chọn **Create role**.

![Kiểm tra và tạo role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/7-create-ecs-task-role.png?featherlight=false&width=90pc)

{{% notice note %}}
Với môi trường production, thay `AmazonS3FullAccess` bằng policy tùy chỉnh chỉ cho phép `s3:PutObject` trên ARN bucket cụ thể. Tương tự, `SecretsManagerReadWrite` nên được thu hẹp xuống `secretsmanager:GetSecretValue` cho đúng secret ARN.
{{% /notice %}}

---

### 5. Kiểm tra các role

Quay lại **IAM → Roles** và xác nhận cả bốn role đã tồn tại:

```text
playwright-lambda-role
playwright-postprocessing-role
playwright-ecs-execution-role
playwright-ecs-task-role
```

![Danh sách IAM role đã tạo](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/9-lambda-role-policies.png?featherlight=false&width=90pc)

Khi tạo ECS task definition ở bước sau, cấu hình:

| Trường | IAM role |
| --- | --- |
| Task execution role | `playwright-ecs-execution-role` |
| Task role | `playwright-ecs-task-role` |

{{% notice info %}}
**Lưu ý quan trọng về việc gán Role cho Lambda:**

- **Hàm `playwright-postprocessing`**: Bắt buộc dùng **`playwright-postprocessing-role`** (role riêng biệt được tạo ở phần 2).
- **Các hàm Lambda còn lại** (`playwright-api-backend`, `playwright-coordinator`, `playwright-error-handler`): Sử dụng role chung là **`playwright-lambda-role`** (đã tạo ở phần 1).
{{% /notice %}}
