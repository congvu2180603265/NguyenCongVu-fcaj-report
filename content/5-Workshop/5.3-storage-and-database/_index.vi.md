---
title : "Lưu trữ & Cơ sở dữ liệu"
date : 2026-07-10
weight : 3
chapter : false
pre : " <b> 5.3. </b> "
---

#### Tạo bảng DynamoDB lưu lịch sử test và log lỗi

Hệ thống dùng 2 bảng DynamoDB: một bảng lưu lịch sử mỗi lần chạy test, một bảng lưu lỗi hệ thống khi có message rơi vào Dead Letter Queue.

**Bước 1:** Truy cập vào **AWS Management Console**, tìm kiếm **DynamoDB** và chọn **DynamoDB**.

![Truy cập DynamoDB Console](/images/5-Workshop/5.3-storage-and-database/1-access-dynamodb-console.png?featherlight=false&width=90pc)

**Bước 2:** Bấm **Create table**.

![Bấm Create table](/images/5-Workshop/5.3-storage-and-database/2-create-table-button.png?featherlight=false&width=90pc)

**Bước 3:** Điền **Table name** = `playwright-test-history`.

**Bước 4:** Điền **Partition key** = `task_id`, kiểu **String**.

![Tạo bảng playwright-test-history](/images/5-Workshop/5.3-storage-and-database/3-create-test-history-table.png?featherlight=false&width=90pc)

**Bước 5:** Các cài đặt khác để mặc định, bấm **Create table**.

![Create table playwright-test-history](/images/5-Workshop/5.3-storage-and-database/4-create-test-history-table.png?featherlight=false&width=90pc)

**Bước 6:** Bấm **Create table** một lần nữa để tạo bảng thứ hai.

**Bước 7:** Điền **Table name** = `playwright-error-log`.

**Bước 8:** Điền **Partition key** = `error_id`, kiểu **String**.

![Tạo bảng playwright-error-log](/images/5-Workshop/5.3-storage-and-database/5-create-error-log-table.png?featherlight=false&width=90pc)

**Bước 9:** Bấm **Create table**.

![Create table playwright-error-log](/images/5-Workshop/5.3-storage-and-database/6-create-error-log-table.png?featherlight=false&width=90pc)

**Bước 10:** Bấm **Create table** lần thứ ba, đặt tên `playwright-email-config`, Partition key = `email_address`, kiểu **String**, bấm **Create table**.
 
![Tạo bảng playwright-email-config](/images/5-Workshop/5.3-storage-and-database/11-create-email-config-table.png?featherlight=false&width=90pc)
 
**Bước 11:** Bấm **Create table** lần thứ tư, đặt tên `playwright-test-suites`, Partition key = `suite_id`, kiểu **String**, bấm **Create table**.
 
![Tạo bảng playwright-test-suites](/images/5-Workshop/5.3-storage-and-database/12-create-test-suites-table.png?featherlight=false&width=90pc)

**Bước 12:** Chờ cả 4 bảng chuyển trạng thái sang **Active**.

![Cả hai bảng ở trạng thái Active](/images/5-Workshop/5.3-storage-and-database/7-tables-active.png?featherlight=false&width=90pc)

*(Ghi chú: Không cần tạo thêm field nào khác như `target_url`, `status`, `report_url`... — DynamoDB là NoSQL, tự nhận field khi ứng dụng ghi dữ liệu vào, không cần khai báo schema cứng trước.)*

#### Cấp quyền cho container Fargate ghi trực tiếp vào DynamoDB

Vì container Fargate tự ghi trạng thái `running/success/failed` trực tiếp vào bảng `playwright-test-history` (không qua Lambda), cần cấp thêm quyền cho Task Role.

**Bước 13:** Truy cập vào **AWS Management Console**, tìm kiếm **IAM**, chọn **IAM**, sau đó chọn **Roles** và tìm kiếm role `playwright-ecs-task-role`.

![tìm role `playwright-ecs-task-role](/images/5-Workshop/5.3-storage-and-database/8-search-playwright-ecs-task-role.png?featherlight=false&width=90pc)

**Bước 14:** Bấm **Add permissions → Create inline policy**.

![Tạo inline policy](/images/5-Workshop/5.3-storage-and-database/9-create-inline-policy.png?featherlight=false&width=90pc)

**Bước 15:** Chọn tab **JSON**, dán nội dung sau:
```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": ["dynamodb:PutItem", "dynamodb:UpdateItem"],
    "Resource": "arn:aws:dynamodb:ap-southeast-1:<account-id>:table/playwright-test-history"
  }]
}
```

**Bước 16:** Thay `<account-id>` bằng Account ID thật (12 chữ số, xem ở góc trên phải Console).

**Bước 17:** Đặt tên policy, ví dụ `DynamoDBWriteAccess`, bấm **Create policy**.

![Policy đã được gắn vào role](/images/5-Workshop/5.3-storage-and-database/10-inline-policy-attached.png?featherlight=false&width=90pc)

#### Kiểm tra

- Cả 4 bảng ở trạng thái **Active** trên Console.
- `playwright-ecs-task-role` hiển thị inline policy `DynamoDBWriteAccess` trong tab Permissions.
- Sau khi chạy thử 1 test: vào bảng `playwright-test-history` → tab **Explore table items** → thấy bản ghi có đủ `status`, `report_url`, `ai_summary`.

---

Các bước khởi tạo Lưu trữ & Cơ sở dữ liệu đã hoàn tất. Tiếp theo, chúng ta sẽ chuyển sang **[5.4. Hàng đợi & VPC Endpoints](../5.4-queue-and-endpoints/)** để khởi tạo SQS queue và cấu hình các VPC Endpoint bảo mật.
