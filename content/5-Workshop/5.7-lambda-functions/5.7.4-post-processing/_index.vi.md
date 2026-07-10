---
title : "Lambda Post-processing & AI"
date : 2026-07-10
weight : 4
chapter : false
pre : " <b> 5.7.4. </b> "
---

#### Triển khai Lambda Post-processing & AI

Trong phần này, chúng ta sẽ triển khai Lambda Function **playwright-postprocessing**. Đây là thành phần chịu trách nhiệm xử lý kết quả sau khi Amazon ECS Fargate hoàn thành quá trình chạy kiểm thử tự động bằng Playwright.

Lambda Post-processing thực hiện các nhiệm vụ chính sau:

- Đọc log kiểm thử từ Amazon CloudWatch Logs.
- Thu thập trạng thái và kết quả của ECS Task.
- Gọi AI API để phân tích và tóm tắt lỗi.
- Tạo báo cáo kiểm thử dưới định dạng HTML hoặc JSON.
- Upload báo cáo lên Amazon S3.
- Tạo Presigned URL để truy cập báo cáo.
- Cập nhật kết quả vào Amazon DynamoDB.
- Gửi email thông báo thông qua Amazon SES.

---

### Bước 1: Truy cập AWS Lambda

Đăng nhập vào **AWS Management Console**.

Tại thanh tìm kiếm, nhập:

```text
Lambda
```

Chọn dịch vụ **AWS Lambda**, sau đó nhấn **Create function**.

![Tạo Lambda Function](/images/5-Workshop/5.7-lambda-functions/5.7.4-post-processing/create-lambda.png?featherlight=false&width=90pc)

---

### Bước 2: Khai báo thông tin Lambda

Trong giao diện **Create function**, chọn:

```text
Author from scratch
```

Nhập các thông tin sau:

| Thuộc tính | Giá trị |
|---|---|
| Function name | `playwright-postprocessing` |
| Runtime | `Python 3.12` |
| Architecture | `x86_64` |

![Cấu hình thông tin Lambda](/images/5-Workshop/5.7-lambda-functions/5.7.4-post-processing/lambda-config.png?featherlight=false&width=90pc)

Trong phần **Permissions** hoặc **Additional settings**, chọn IAM Role đã tạo trước đó:

```text
playwright-lambda-role
```

IAM Role này cần có quyền truy cập đến:

- Amazon CloudWatch Logs
- Amazon S3
- Amazon DynamoDB
- AWS Secrets Manager
- Amazon SES
- Amazon ECS

Sau khi kiểm tra đầy đủ thông tin, chọn **Create function**.

---

### Bước 3: Cập nhật mã nguồn Lambda

Sau khi Lambda được tạo thành công, mở tab **Code**.

Trong phần **Code source**, cập nhật file:

```text
lambda_function.py
```

Nếu mã nguồn có thêm template báo cáo, cấu trúc có thể bao gồm:

```text
lambda_function.py
templates/
├── email_report.html
└── style.css
```

Sau khi hoàn tất, nhấn **Deploy** để lưu mã nguồn.

![Cập nhật mã nguồn Lambda](/images/5-Workshop/5.7-lambda-functions/5.7.4-post-processing/upload-code.png?featherlight=false&width=90pc)

{{% notice note %}}
Cần nhấn **Deploy** sau mỗi lần thay đổi mã nguồn. Nếu không, Lambda vẫn chạy phiên bản code trước đó.
{{% /notice %}}

---

### Bước 4: Cấu hình Environment Variables

Mở:

```text
Configuration
→ Environment variables
→ Edit
```

Thêm các biến môi trường sau:

| Key | Value |
|---|---|
| `LOG_GROUP_NAME` | `/ecs/playwright-runner` |
| `TEST_HISTORY_TABLE` | `playwright-test-history` |
| `REPORT_BUCKET` | `playwright-report-2026` |
| `OPENAI_SECRET_NAME` | `playwright/openai-api-key` |
| `SES_SENDER_EMAIL` | Email đã xác thực trong Amazon SES |

![Cấu hình Environment Variables](/images/5-Workshop/5.7-lambda-functions/5.7.4-post-processing/env-vars.png?featherlight=false&width=90pc)

Ý nghĩa của từng biến:

- `LOG_GROUP_NAME`: tên CloudWatch Log Group chứa log của ECS Fargate.
- `TEST_HISTORY_TABLE`: bảng DynamoDB lưu lịch sử kiểm thử.
- `REPORT_BUCKET`: bucket S3 dùng để lưu báo cáo.
- `OPENAI_SECRET_NAME`: tên secret chứa AI API Key trong Secrets Manager.
- `SES_SENDER_EMAIL`: email người gửi đã được xác thực trong Amazon SES.


Sau khi nhập đầy đủ, nhấn **Save**.

---

### Bước 5: Cấu hình VPC

Mở:

```text
Configuration
→ VPC
→ Edit
```

Cấu hình các thông tin:

| Thuộc tính | Giá trị |
|---|---|
| VPC | `playwright-vpc` |
| Subnet | `playwright-private-subnet` |
| Security Group | `playwright-sg-lambda` |

![Cấu hình VPC cho Lambda](/images/5-Workshop/5.7-lambda-functions/5.7.4-post-processing/vpc-config.png?featherlight=false&width=90pc)

Lambda được đặt trong Private Subnet để truy cập các tài nguyên nội bộ thông qua VPC Endpoint hoặc NAT Gateway.

{{% notice info %}}
AWS có thể hiển thị cảnh báo nên sử dụng ít nhất hai subnet để đảm bảo High Availability. Trong phạm vi bài workshop, một Private Subnet vẫn có thể được sử dụng.
{{% /notice %}}

Nhấn **Save** để lưu cấu hình.

---

### Bước 6: Cấu hình Timeout

Mở:

```text
Configuration
→ General configuration
→ Edit
```

Thiết lập:

```text
Timeout = 30 seconds
```

![Cấu hình Timeout](/images/5-Workshop/5.7-lambda-functions/5.7.4-post-processing/timeout-configuration.png?featherlight=false&width=90pc)

Lambda Post-processing cần nhiều thời gian hơn Lambda thông thường vì phải:

- Đọc CloudWatch Logs.
- Gọi AI API.
- Tạo báo cáo.
- Upload file lên S3.
- Cập nhật DynamoDB.
- Gửi email qua SES.

Sau đó nhấn **Save**.

---

### Bước 7: Tạo EventBridge Rule

Lambda Post-processing không được kích hoạt từ Amazon SQS. Lambda này được gọi khi ECS Task hoàn thành và chuyển sang trạng thái `STOPPED`.

Truy cập:

```text
Amazon EventBridge
→ Rules
→ Create rule
```

Cấu hình:

| Thuộc tính | Giá trị |
|---|---|
| Rule name | `playwright-postprocessing-rule` |
| Rule type | `Rule with an event pattern` |
| Event source | `AWS events` |
| AWS service | `Elastic Container Service` |
| Event type | `ECS Task State Change` |

![Tạo EventBridge Rule](/images/5-Workshop/5.7-lambda-functions/5.7.4-post-processing/create-eventbridge-rule.png?featherlight=false&width=90pc)

---

### Bước 8: Khai báo Event Pattern

Trong phần **Event pattern**, nhập nội dung:

```json
{
  "source": ["aws.ecs"],
  "detail-type": ["ECS Task State Change"],
  "detail": {
    "lastStatus": ["STOPPED"],
    "clusterArn": [
      "arn:aws:ecs:ap-southeast-1:238337501662:cluster/playwright-cluster"
    ]
  }
}
```

{{% notice warning %}}
Thay AWS Account ID và Cluster ARN bằng thông tin thực tế trong tài khoản AWS đang sử dụng.
{{% /notice %}}

Việc lọc theo `clusterArn` giúp tránh trường hợp Lambda bị gọi bởi các ECS Task không liên quan trong cùng tài khoản.

---

### Bước 9: Chọn Target cho EventBridge

Ở phần **Target**, chọn:

| Thuộc tính | Giá trị |
|---|---|
| Target type | `AWS service` |
| Select a target | `Lambda function` |
| Function | `playwright-postprocessing` |

Hoàn tất quá trình tạo Rule.

Quay lại Lambda:

```text
Lambda
→ playwright-postprocessing
→ Configuration
→ Triggers
```

Kiểm tra trigger EventBridge đã xuất hiện.

---

### Bước 10: Kiểm tra Lambda hoàn chỉnh

Sau khi hoàn tất, Lambda cần đáp ứng các điều kiện sau:

- Function name là `playwright-postprocessing`.
- Runtime là Python 3.12.
- IAM Role là `playwright-lambda-role`.
- Lambda nằm trong `playwright-vpc`.
- Sử dụng `playwright-private-subnet`.
- Gắn Security Group `playwright-sg-lambda`.
- Timeout là 30 giây.
- Có đầy đủ năm biến môi trường.
- Có EventBridge Trigger theo trạng thái ECS Task `STOPPED`.

---

### Kết quả mong đợi

Khi ECS Fargate hoàn thành bài kiểm thử, quy trình xử lý sẽ diễn ra như sau:

```text
ECS Fargate Task
        ↓
Task chuyển sang trạng thái STOPPED
        ↓
Amazon EventBridge
        ↓
Lambda playwright-postprocessing
        ↓
Đọc CloudWatch Logs
        ↓
Gọi AI API để phân tích lỗi
        ↓
Tạo báo cáo HTML/JSON
        ↓
Upload báo cáo lên Amazon S3
        ↓
Cập nhật DynamoDB
        ↓
Gửi email qua Amazon SES
```

Sau khi Lambda chạy thành công, bảng `playwright-test-history` sẽ được cập nhật các trường:

```text
status
finished_at
report_url
ai_summary
```

Báo cáo được lưu trong bucket:

```text
playwright-report-2026
```

và người dùng có thể truy cập thông qua Presigned URL do Lambda tạo ra.
