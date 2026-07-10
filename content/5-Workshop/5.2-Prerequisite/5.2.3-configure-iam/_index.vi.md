---
title : "Cấu hình IAM"
date : 2026-07-10
weight : 3
chapter : false
pre : " <b> 5.2.3. </b> "
---

#### Cấu hình IAM Role cho Lambda và ECS

Trong phần này, chúng ta sẽ tạo ba IAM role phục vụ hệ thống kiểm thử Playwright:

| IAM role | Trusted service | Mục đích |
|---|---|---|
| `playwright-lambda-role` | AWS Lambda | Cho phép Lambda điều phối ECS và xử lý kết quả |
| `playwright-ecs-execution-role` | ECS Tasks | Cho phép ECS tải image từ ECR và ghi log |
| `playwright-ecs-task-role` | ECS Tasks | Cấp quyền cho ứng dụng chạy bên trong container |

{{% notice warning %}}
Tuân thủ nguyên tắc đặc quyền tối thiểu. Các policy `FullAccess` xuất hiện trong ảnh chỉ phù hợp để minh họa trong môi trường workshop. Không cấp `IAMFullAccess` cho Lambda hoặc ECS task trong môi trường thực tế.
{{% /notice %}}

---

### Bước 1: Tạo Lambda execution role

Mở **AWS Management Console**, tìm **IAM**, sau đó chọn:

```text
Roles
→ Create role
```

Chọn **AWS service** làm trusted entity và chọn use case **Lambda**.

Tại bước đặt tên, nhập:

```text
playwright-lambda-role
```

![Tạo Lambda role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/create-lambda-role.png?featherlight=false&width=90pc)

Lambda hậu xử lý cần các nhóm quyền sau:

- Ghi log vào CloudWatch Logs.
- Đọc trạng thái ECS task.
- Đọc log của ECS task.
- Đọc và ghi báo cáo trong bucket `playwright-report-2026`.
- Cập nhật bảng DynamoDB của workshop.
- Đọc secret `playwright/openai-api-key`.
- Gửi email bằng Amazon SES.

![Các policy của Lambda role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/lambda-role-created.png?featherlight=false&width=90pc)

{{% notice note %}}
Trong production, hãy thay các AWS managed policy dạng `FullAccess` bằng custom policy giới hạn theo action và ARN tài nguyên. Quyền Secrets Manager chỉ cần `secretsmanager:GetSecretValue`; không cần `SecretsManagerReadWrite`.
{{% /notice %}}

Sau khi kiểm tra trust policy có principal `lambda.amazonaws.com`, chọn **Create role**.

---

### Bước 2: Tạo ECS task execution role

Tạo role mới và chọn:

| Thuộc tính | Giá trị |
|---|---|
| Trusted entity type | `AWS service` |
| Service or use case | `Elastic Container Service` |
| Use case | `Elastic Container Service Task` |

![Chọn ECS task làm trusted service](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/create-ecs-execution-role.png?featherlight=false&width=90pc)

Gắn AWS managed policy:

```text
AmazonECSTaskExecutionRolePolicy
```

Policy này cho phép ECS agent tải image từ Amazon ECR và gửi container log đến CloudWatch Logs.

![Policy của ECS execution role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/ecs-execution-role-created.png?featherlight=false&width=90pc)

Đặt tên role:

```text
playwright-ecs-execution-role
```

![Đặt tên ECS execution role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/playwright-ecs-execution-role.png?featherlight=false&width=90pc)

Kiểm tra trust policy sử dụng service principal `ecs-tasks.amazonaws.com`, sau đó chọn **Create role**.

---

### Bước 3: Tạo ECS task role

Tạo một role khác với cùng trusted service:

```text
Elastic Container Service Task
```

![Tạo ECS task role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/ecs-task-role-created.png?featherlight=false&width=90pc)

Task role được ứng dụng Playwright bên trong container sử dụng. Chỉ cấp những quyền mà mã nguồn thực sự cần, chẳng hạn:

- Đọc kịch bản kiểm thử từ bucket báo cáo.
- Ghi báo cáo, ảnh chụp và kết quả kiểm thử vào đúng bucket/prefix.
- Ghi application log nếu ứng dụng gọi CloudWatch Logs trực tiếp.

Ảnh workshop minh họa việc chọn policy S3 và CloudWatch Logs:

![Các policy của ECS task role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/create-ecs-task-role.png?featherlight=false&width=90pc)

Đặt tên role:

```text
playwright-ecs-task-role
```

![Đặt tên ECS task role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/ecs-task-role-policies.png?featherlight=false&width=90pc)

Sau đó chọn **Create role**.

---

### Bước 4: Kiểm tra các role đã tạo

Quay lại **IAM → Roles** và xác nhận có đủ ba role:

```text
playwright-lambda-role
playwright-ecs-execution-role
playwright-ecs-task-role
```

![Danh sách IAM role đã tạo](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/lambda-role-policies.png?featherlight=false&width=90pc)

Khi tạo ECS task definition ở phần sau, cấu hình:

| Trường | IAM role |
|---|---|
| Task execution role | `playwright-ecs-execution-role` |
| Task role | `playwright-ecs-task-role` |

Lambda `playwright-postprocessing` sử dụng `playwright-lambda-role` làm execution role.
