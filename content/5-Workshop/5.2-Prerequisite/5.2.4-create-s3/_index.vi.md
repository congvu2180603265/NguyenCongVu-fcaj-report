---
title : "Khởi tạo S3"
date : 2026-06-13
weight : 4
chapter : false
pre : " <b> 5.2.4. </b> "
---

#### Tạo các bucket Amazon S3

Trong phần này, chúng ta sẽ tạo hai bucket S3 tại Region **Asia Pacific (Singapore) (`ap-southeast-1`)**:

| Bucket | Mục đích |
|---|---|
| `playwright-webui-12` | Lưu các tệp frontend của Dashboard Console |
| `playwright-report-2026` | Lưu báo cáo, ảnh chụp và dữ liệu kết quả kiểm thử |

{{% notice note %}}
Tên bucket S3 phải là duy nhất. Nếu các tên trong hướng dẫn đã được sử dụng, hãy thêm hậu tố riêng và dùng cùng tên đó ở các bước cấu hình sau.
{{% /notice %}}

---

**Bước 1:** Tạo bucket Web UI

Đăng nhập **AWS Management Console**, mở **Amazon S3**, sau đó chọn **Create bucket**.

Cấu hình:

| Thuộc tính | Giá trị |
|---|---|
| AWS Region | `ap-southeast-1` |
| Bucket type | `General purpose` |
| Bucket namespace | `Global namespace` |
| Bucket name | `playwright-webui-12` |
| Object Ownership | `ACLs disabled` |

Giữ các thiết lập còn lại theo mặc định và chọn **Create bucket**.

![Tạo bucket Web UI](/images/5-Workshop/5.2-Prerequisite/5.2.4-create-s3/create-webui-bucket.png?featherlight=false&width=90pc)

---

**Bước 2:** Tải mã nguồn frontend lên S3

Mở bucket `playwright-webui-12`, chọn **Upload**, rồi tải toàn bộ nội dung bên trong thư mục build `dist` lên thư mục gốc của bucket. Không tải chính thư mục `dist` làm thư mục cha.

Sau khi tải lên, bucket cần có `index.html` và các tài nguyên frontend, ví dụ:

```text
assets/
favicon.svg
icons.svg
index.html
```

![Các tệp trong bucket Web UI](/images/5-Workshop/5.2-Prerequisite/5.2.4-create-s3/webui-bucket-objects.png?featherlight=false&width=90pc)

---

**Bước 3:** Cấu hình static website hosting

Trong bucket Web UI, mở:

```text
Properties
→ Static website hosting
→ Edit
```

Chọn:

| Thuộc tính | Giá trị |
|---|---|
| Static website hosting | `Enable` |
| Hosting type | `Host a static website` |
| Index document | `index.html` |

Nếu frontend là ứng dụng SPA, có thể đặt **Error document** thành `index.html`. Sau đó chọn **Save changes**.

![Bật static website hosting](/images/5-Workshop/5.2-Prerequisite/5.2.4-create-s3/edit-static.png?featherlight=false&width=90pc)

{{% notice warning %}}
Không public bucket báo cáo. Với môi trường production, nên giữ bucket Web UI ở chế độ private và cho CloudFront truy cập bằng Origin Access Control (OAC). Khi dùng OAC, hãy dùng S3 bucket origin thay vì S3 website endpoint.
{{% /notice %}}

---

**Bước 4:** Tạo bucket lưu báo cáo

Quay lại danh sách S3 bucket, chọn **Create bucket** và cấu hình:

| Thuộc tính | Giá trị |
|---|---|
| AWS Region | `ap-southeast-1` |
| Bucket type | `General purpose` |
| Bucket namespace | `Global namespace` |
| Bucket name | `playwright-report-2026` |
| Block Public Access | Giữ bật tất cả tùy chọn |

Giữ các thiết lập còn lại theo mặc định và chọn **Create bucket**.

![Tạo bucket báo cáo](/images/5-Workshop/5.2-Prerequisite/5.2.4-create-s3/create-report-bucket.png?featherlight=false&width=90pc)

---

**Bước 5:** Tạo cấu trúc lưu trữ báo cáo

Mở bucket `playwright-report-2026` và tạo các thư mục:

```text
json/
reports/
screenshots/
test-scripts/
```

Các thư mục này lần lượt lưu kết quả JSON, báo cáo HTML, ảnh chụp khi kiểm thử và kịch bản kiểm thử.

![Cấu trúc bucket báo cáo](/images/5-Workshop/5.2-Prerequisite/5.2.4-create-s3/report-bucket-objects.png?featherlight=false&width=90pc)

---

**Bước 6:** Kiểm tra kết quả

Quay lại **Amazon S3 → Buckets** và xác nhận hai bucket đã được tạo trong `ap-southeast-1`:

```text
playwright-webui-12
playwright-report-2026
```

![Xác nhận các bucket đã tạo](/images/5-Workshop/5.2-Prerequisite/5.2.4-create-s3/verify-buckets.png?featherlight=false&width=90pc)

Sau khi hoàn tất, bucket Web UI sẵn sàng cho bước cấu hình CloudFront và bucket báo cáo sẵn sàng để Lambda lưu kết quả kiểm thử.
