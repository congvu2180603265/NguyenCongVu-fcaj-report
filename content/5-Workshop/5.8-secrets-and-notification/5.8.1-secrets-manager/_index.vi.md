---
title : "AI API Key"
date : 2026-06-19
weight : 1
chapter : false
pre : " <b> 5.8.1. </b> "
---

#### Lưu AI API Key bằng AWS Secrets Manager

Trong phần này, chúng ta sẽ tạo một **AWS Secrets Manager Secret** để lưu trữ an toàn AI API Key được sử dụng bởi Lambda **playwright-postprocessing**.

Thay vì lưu trực tiếp API Key trong mã nguồn của Lambda, hàm sẽ truy xuất khóa từ AWS Secrets Manager mỗi khi được thực thi.

Việc sử dụng Secrets Manager mang lại các lợi ích sau:

- Lưu trữ API Key một cách an toàn.
- Mã hóa khóa bằng AWS Key Management Service (KMS).
- Tránh lộ thông tin nhạy cảm trong mã nguồn.
- Cho phép Lambda truy xuất khóa khi chạy.
- Dễ dàng quản lý và thay đổi API Key trong tương lai.

---

**Bước 1:** Mở AWS Secrets Manager

Đăng nhập vào **AWS Management Console**.

Tại thanh tìm kiếm, nhập:

```text
Secrets Manager
```

Chọn **AWS Secrets Manager**, sau đó nhấn:

```text
Store a new secret
```

![Mở AWS Secrets Manager](/images/5-Workshop/5.8-secrets-and-notification/5.8.1-secrets-manager/01-store-new-secret.png?featherlight=false&width=90pc)

---

**Bước 2:** Chọn loại Secret

Tại trang **Choose secret type**, chọn:

```text
Other type of secret
```

Đây là loại Secret dùng để lưu các thông tin tùy chỉnh như API Key, Token hoặc Password.

Trong phần **Key/value pairs**, nhập:

| Key | Value |
|---|---|
| api_key | AI API Key của bạn |

Giữ nguyên khóa mã hóa mặc định:

```text
aws/secretsmanager
```

Sau đó chọn **Next**.

![Chọn loại Secret và nhập API Key](/images/5-Workshop/5.8-secrets-and-notification/5.8.1-secrets-manager/02-configure-secret-value.png?featherlight=false&width=90pc)

{{% notice warning %}}
Không đưa AI API Key thật lên GitHub hoặc trong báo cáo. Hãy làm mờ (blur) hoặc che phần Value trước khi chụp ảnh.
{{% /notice %}}

---

**Bước 3:** Đặt tên Secret

Nhập các thông tin sau:

| Thuộc tính | Giá trị |
|---|---|
| Secret name | `playwright/openai-api-key` |
| Description | AI API Key for Playwright Lambda |

Sau đó chọn **Next**.

![Đặt tên Secret](/images/5-Workshop/5.8-secrets-and-notification/5.8.1-secrets-manager/03-configure-secret-name.png?featherlight=false&width=90pc)

---

**Bước 4:** Cấu hình Rotation

Trong Workshop này chưa cần sử dụng tính năng tự động thay đổi API Key.

Giữ nguyên cấu hình:

```text
Disable automatic rotation
```

Sau đó chọn **Next**.

---

**Bước 5:** Kiểm tra và lưu Secret

Kiểm tra lại các thông tin:

| Thuộc tính | Giá trị |
|---|---|
| Secret Type | Other type of secret |
| Secret Name | playwright/openai-api-key |
| Encryption Key | aws/secretsmanager |

Sau đó nhấn:

```text
Store
```

---

**Bước 6:** Kiểm tra Secret đã tạo

Sau khi tạo thành công, mở:

```text
AWS Secrets Manager
→ Secrets
→ playwright/openai-api-key
```

Màn hình sẽ hiển thị các thông tin:

- Secret name
- Secret ARN
- Encryption key

![Secret đã được tạo](/images/5-Workshop/5.8-secrets-and-notification/5.8.1-secrets-manager/04-secret-created.png?featherlight=false&width=90pc)

{{% notice note %}}
Không nhấn **Retrieve secret value** khi chụp ảnh vì API Key sẽ được hiển thị.
{{% /notice %}}

---

**Bước 7:** Cấu hình Lambda sử dụng Secret

Mở:

```text
AWS Lambda
→ playwright-postprocessing
→ Configuration
→ Environment variables
→ Edit
```

Kiểm tra hoặc thêm biến môi trường:

| Key | Value |
|---|---|
| OPENAI_SECRET_NAME | playwright/openai-api-key |

Lambda sẽ sử dụng biến môi trường này để xác định Secret cần truy xuất.

---

**Bước 8:** Đọc Secret trong Lambda

Lambda sử dụng đoạn mã Python dưới đây để lấy AI API Key từ AWS Secrets Manager.

```python
import json
import boto3
import os

secrets_client = boto3.client("secretsmanager")

def get_api_key():

    secret_name = os.environ["OPENAI_SECRET_NAME"]

    response = secrets_client.get_secret_value(
        SecretId=secret_name
    )

    secret = json.loads(response["SecretString"])

    return secret["api_key"]
```

Đoạn mã trên sẽ tự động lấy API Key mới nhất mỗi khi Lambda được thực thi.

---

**Bước 9:** Cấp quyền cho Lambda

IAM Role của Lambda cần có quyền đọc Secret:

```json
{
    "Effect": "Allow",
    "Action": [
        "secretsmanager:GetSecretValue"
    ],
    "Resource": "arn:aws:secretsmanager:*:*:secret:playwright/openai-api-key-*"
}
```

Quyền này cho phép Lambda chỉ truy cập đúng Secret cần sử dụng.

---

#### Kết quả mong đợi

Sau khi hoàn thành, luồng hoạt động sẽ như sau:

```text
AWS Secrets Manager
        │
playwright/openai-api-key
        │
Mã hóa bằng AWS KMS
        │
Biến môi trường
OPENAI_SECRET_NAME
        │
Lambda playwright-postprocessing
        │
Lấy API Key
        │
Gọi AI API
```

Sau khi hoàn thành phần này:

- AI API Key được lưu an toàn trong AWS Secrets Manager.
- Lambda không cần lưu API Key trong mã nguồn.
- API Key chỉ được truy xuất khi Lambda thực thi.
- Dễ dàng thay đổi hoặc cập nhật API Key mà không cần chỉnh sửa mã nguồn.
---

Tiếp theo, chúng ta sẽ chuyển sang **[5.8.2. Thông báo qua SES Email](../5.8.2-notification/)** để cấu hình kênh gửi email thông báo.
