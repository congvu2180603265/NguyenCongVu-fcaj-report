---
title : "Khởi tạo ECR Repository"
date : 2026-06-13
weight : 5
chapter : false
pre : " <b> 5.2.5. </b> "
---

#### Tạo Amazon ECR Repository

Trong phần này, chúng ta sẽ tạo một private repository trên **Amazon Elastic Container Registry (Amazon ECR)** để lưu Docker image của Playwright Runner. Amazon ECS Fargate sẽ lấy image này khi khởi chạy tác vụ kiểm thử.

Thông tin repository:

| Thuộc tính | Giá trị |
| --- | --- |
| AWS Region | `ap-southeast-1` |
| Visibility | `Private` |
| Repository name | `playwright-runner` |
| Tag mutability | `Mutable` |
| Encryption | `AES-256` |
| Image scanning | Scan on push |

---

**Bước 1:** Mở Amazon ECR

Đăng nhập **AWS Management Console**, tìm **Elastic Container Registry**, sau đó mở:

```text
Private registry
→ Repositories
→ Create repository
```

Chọn **Private** và nhập tên repository:

```text
playwright-runner
```

Giữ **Image tag mutability** là `Mutable`, bật quét image khi push và giữ mã hóa `AES-256`. Sau đó chọn **Create repository**.

![Cấu hình ECR repository](/images/5-Workshop/5.2-Prerequisite/5.2.5-create-ecr/create-repository.png?featherlight=false&width=90pc)

{{% notice note %}}
`Mutable` phù hợp với workshop vì tag `latest` có thể được cập nhật. Trong production, nên dùng tag phiên bản bất biến như Git commit SHA và cân nhắc chọn `Immutable`.
{{% /notice %}}

---

**Bước 2:** Kiểm tra repository đã tạo

Sau khi tạo thành công, danh sách **Private repositories** sẽ hiển thị `playwright-runner` cùng URI, chế độ tag và loại mã hóa.

![Repository đã được tạo](/images/5-Workshop/5.2-Prerequisite/5.2.5-create-ecr/repository-created.png?featherlight=false&width=90pc)

Mở repository và kiểm tra:

- Repository name: `playwright-runner`
- Tag mutability: `Mutable`
- Encryption type: `AES-256`
- Scan frequency: `Scan on push`

![Chi tiết repository](/images/5-Workshop/5.2-Prerequisite/5.2.5-create-ecr/repository-details.png?featherlight=false&width=90pc)

---

**Bước 3:** Xem lệnh push image

Trong repository, chọn **View push commands**, sau đó chọn tab **Windows**. AWS sẽ hiển thị các lệnh dành riêng cho tài khoản và Region hiện tại.

![Các lệnh push trên Windows](/images/5-Workshop/5.2-Prerequisite/5.2.5-create-ecr/view-push-commands-windows.png?featherlight=false&width=90pc)

---

**Bước 4:** Đăng nhập Docker vào ECR

Mở PowerShell tại thư mục chứa `Dockerfile`. Thay `<ACCOUNT_ID>` bằng AWS Account ID của bạn rồi chạy:

```powershell
aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin <ACCOUNT_ID>.dkr.ecr.ap-southeast-1.amazonaws.com
```

Kết quả mong đợi:

```text
Login Succeeded
```

{{% notice warning %}}
Không đưa mật khẩu, access key hoặc token đăng nhập ECR vào mã nguồn. Token do lệnh đăng nhập tạo ra chỉ có hiệu lực tạm thời.
{{% /notice %}}

---

**Bước 5:** Build, tag và push Docker image

Build image từ `Dockerfile`:

```powershell
docker build -t playwright-runner .
```

Gắn tag theo ECR repository URI:

```powershell
docker tag playwright-runner:latest <ACCOUNT_ID>.dkr.ecr.ap-southeast-1.amazonaws.com/playwright-runner:latest
```

Push image lên ECR:

```powershell
docker push <ACCOUNT_ID>.dkr.ecr.ap-southeast-1.amazonaws.com/playwright-runner:latest
```

---

**Bước 6:** Xác nhận image

Quay lại repository `playwright-runner`, mở tab **Images**, sau đó làm mới trang. Image tag `latest` cần xuất hiện và trạng thái quét không có lỗ hổng nghiêm trọng chưa xử lý.

Repository URI có dạng:

```text
<ACCOUNT_ID>.dkr.ecr.ap-southeast-1.amazonaws.com/playwright-runner:latest
```

URI này sẽ được sử dụng khi tạo ECS task definition cho Playwright Runner.

{{% notice info %}}
**Mã nguồn Playwright (GitHub)**

Mã nguồn kiểm thử và file `Dockerfile` để build image cho ECR này đã được chuẩn bị sẵn. Bạn hãy clone repository sau về máy để sử dụng ở phần 5.5 nhé:
[https://github.com/VanPhuc-027/playwright-runner.git](https://github.com/VanPhuc-027/playwright-runner.git)
{{% /notice %}}

---

Tiếp theo, chúng ta sẽ chuyển sang **[5.3. Lưu trữ và Cơ sở dữ liệu](../5.3-storage-and-database/)** để khởi tạo S3 Bucket và bảng DynamoDB.
