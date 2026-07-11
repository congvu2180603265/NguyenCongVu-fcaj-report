---
title : "Cấu hình IAM"
date : 2026-06-13
weight : 3
chapter : false
pre : " <b> 5.2.3. </b> "
---

#### Cấu hình IAM Role cho Lambda và ECS

Trong phần này, chúng ta sẽ tạo bốn IAM Role cho hệ thống kiểm thử Playwright:

| IAM Role | Trusted service | Mục đích |
|---|---|---|
| `playwright-lambda-role` | AWS Lambda | Cho phép Lambda điều phối ECS và xử lý kết quả |
| `playwright-postprocessing-role` | AWS Lambda | Role riêng cho Lambda `playwright-postprocessing`, tách biệt để kiểm soát quyền gọi Secrets Manager và SES chặt chẽ hơn |
| `playwright-ecs-execution-role` | ECS Tasks | Cho phép ECS tải image từ ECR và ghi log |
| `playwright-ecs-task-role` | ECS Tasks | Cấp quyền cho ứng dụng bên trong container |

{{% notice warning %}}
Tuân thủ nguyên tắc cấp quyền tối thiểu (least privilege). Các policy `FullAccess` xuất hiện trong ảnh chụp chỉ phù hợp cho mục đích minh họa workshop. Không cấp `IAMFullAccess` cho Lambda hoặc ECS task trong môi trường thực tế.
{{% /notice %}}

---

**Bước 1:** Tạo Lambda execution role

Mở **AWS Management Console**, tìm **IAM**, sau đó chọn:

```text
Roles
→ Create role
```

Chọn **AWS service** làm trusted entity và chọn use case **Lambda**.

Đặt tên role:

```text
playwright-lambda-role
```

![Tạo Lambda role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/1-create-lambda-role.png?featherlight=false&width=90pc)

Lambda xử lý hậu kỳ (post-processing) cần các nhóm quyền sau:

- Ghi log lên CloudWatch Logs.
- Đọc trạng thái ECS task.
- Đọc log ECS task.
- Đọc và ghi báo cáo trong `playwright-report-2026`.
- Cập nhật bảng DynamoDB của workshop.
- Đọc secret `playwright/openai-api-key`.
- Gửi email qua Amazon SES.

![Policy cho Lambda role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/2-lambda-role-created.png?featherlight=false&width=90pc)

{{% notice note %}}
Với môi trường production, hãy thay các policy `FullAccess` của AWS bằng policy tùy chỉnh giới hạn theo action và resource ARN cụ thể. Secrets Manager chỉ cần quyền `secretsmanager:GetSecretValue`; không cần dùng `SecretsManagerReadWrite`.
{{% /notice %}}

Kiểm tra trust policy có chứa service principal `lambda.amazonaws.com`, sau đó chọn **Create role**.

---

**Bước 2:** Tạo role riêng cho Lambda Post-processing

Ngoài `playwright-lambda-role` dùng chung ở Bước 1, Lambda `playwright-postprocessing` sử dụng **một role riêng** — vì hàm này gọi Secrets Manager để lấy AI API Key và gọi SES để gửi email nhiều hơn các Lambda khác trong hệ thống, nên tách riêng để dễ kiểm soát và thu hẹp quyền sau này.

Tạo thêm một role mới, chọn:

| Thuộc tính | Giá trị |
|---|---|
| Trusted entity type | `AWS service` |
| Service or use case | `Lambda` |

Gắn các nhóm quyền tương tự Bước 1 (CloudWatch Logs, DynamoDB, Secrets Manager, S3, SES).

Đặt tên role:

```text
playwright-postprocessing-role
```

![Role playwright-postprocessing-role đã được tạo](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/2b-postprocessing-role-created.jpeg?featherlight=false&width=90pc)

{{% notice note %}}
Ghi lại ARN của role này (dạng `arn:aws:iam::<account-id>:role/playwright-postprocessing-role`) — sẽ dùng để gắn cho Lambda `playwright-postprocessing` ở mục 5.7, thay vì `playwright-lambda-role`.
{{% /notice %}}

Kiểm tra trust policy có chứa service principal `lambda.amazonaws.com`, sau đó chọn **Create role**.

---

**Bước 3:** Tạo ECS task execution role

Tạo thêm một role khác và chọn:

| Thuộc tính | Giá trị |
|---|---|
| Trusted entity type | `AWS service` |
| Service or use case | `Elastic Container Service` |
| Use case | `Elastic Container Service Task` |

![Chọn ECS Tasks làm trusted service](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/3-create-ecs-execution-role.png?featherlight=false&width=90pc)

Gắn AWS managed policy sau:

```text
AmazonECSTaskExecutionRolePolicy
```

Policy này cho phép ECS agent tải image từ Amazon ECR và gửi log container lên CloudWatch Logs.

![Policy cho ECS execution role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/4-ecs-execution-role-created.png?featherlight=false&width=90pc)

Đặt tên role:

```text
playwright-ecs-execution-role
```

![Đặt tên ECS execution role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/5-playwright-ecs-execution-role.png?featherlight=false&width=90pc)

Kiểm tra trust policy dùng service principal `ecs-tasks.amazonaws.com`, sau đó chọn **Create role**.

---

**Bước 4:** Tạo ECS task role

Tạo thêm một role khác với cùng trusted service:

```text
Elastic Container Service Task
```

![Tạo ECS task role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/6-ecs-task-role-created.png?featherlight=false&width=90pc)

Ứng dụng Playwright bên trong container sử dụng task role này. Chỉ cấp đúng những quyền ứng dụng thực sự cần, ví dụ:

- Đọc test script từ report bucket.
- Ghi báo cáo, ảnh chụp và kết quả vào các prefix bucket cần thiết.
- Ghi log ứng dụng nếu ứng dụng gọi trực tiếp CloudWatch Logs.

Ảnh chụp workshop minh họa việc chọn policy S3 và CloudWatch Logs:

![Policy cho ECS task role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/7-create-ecs-task-role.png?featherlight=false&width=90pc)

Đặt tên role:

```text
playwright-ecs-task-role
```

![Đặt tên ECS task role](/images/5-Workshop/5.2-Prerequisite/5.2.3-configure-iam/8-ecs-task-role-policies.png?featherlight=false&width=90pc)

Sau đó chọn **Create role**.

---

**Bước 5:** Kiểm tra các role

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
|---|---|
| Task execution role | `playwright-ecs-execution-role` |
| Task role | `playwright-ecs-task-role` |

Hàm Lambda `playwright-postprocessing` sử dụng **`playwright-postprocessing-role`** làm execution role (không dùng `playwright-lambda-role` dùng chung). Các Lambda còn lại (`playwright-api-backend`, `playwright-coordinator`, `playwright-error-handler`) tiếp tục dùng `playwright-lambda-role`.