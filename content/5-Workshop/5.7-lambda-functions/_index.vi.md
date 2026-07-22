---
title : "Hàm Lambda"
date : 2026-07-10
weight : 7
chapter : false
pre : " <b> 5.7. </b> "
---

#### Tổng quan

Trong phần này, bạn sẽ triển khai bốn hàm AWS Lambda đóng vai trò là lớp điều phối trung tâm của hệ thống kiểm thử Playwright serverless. Các hàm này chịu trách nhiệm xử lý yêu cầu API, khởi chạy ECS Task, quản lý lỗi và xử lý báo cáo sau khi kiểm thử hoàn tất.

Bạn sẽ cấu hình các hàm sau:

1. **playwright-api-backend**: Đóng vai trò là backend cho API Gateway để xử lý yêu cầu từ người dùng.
2. **playwright-coordinator**: Được kích hoạt bởi hàng đợi SQS để khởi chạy các tác vụ ECS Fargate.
3. **playwright-error-handler**: Được kích hoạt bởi Dead Letter Queue (DLQ) để xử lý các thông điệp lỗi.
4. **playwright-postprocessing**: Được kích hoạt bởi EventBridge để xử lý kết quả sau khi một tác vụ ECS hoàn thành.

---

#### 1. Tạo hàm `playwright-api-backend`

Hàm này là điểm tích hợp cho API Gateway của bạn.

**Bước 1:** Truy cập vào **AWS Management Console**, tìm kiếm **Lambda** và chọn **Lambda**.

**Bước 2:** Nhấn **Create function**.

**Bước 3:** Chọn **Author from scratch**.

**Bước 4:** Đặt **Function name** là `playwright-api-backend`.

**Bước 5:** Chọn **Runtime** là `Python 3.14` (hoặc phiên bản Python mới nhất có sẵn).
![Tạo API Backend](/images/5-Workshop/5.7-lambda-functions/create-function-api-backend.png)

**Bước 6:** Dưới mục **Permissions**, mở rộng phần **Change default execution role**.

**Bước 7:** Chọn **Use an existing role** và chọn `playwright-lambda-role` đã được tạo trong phần chuẩn bị.
![Execution Role](/images/5-Workshop/5.7-lambda-functions/create-function-role.png)

**Bước 8:** Nhấn **Create function**.

**Bước 9:** Cuộn xuống phần **Code source**, nhấn **Upload from** và chọn **.zip file**. Tải lên tệp `playwright-api-backend.zip` mà bạn đã chuẩn bị ở phần Chuẩn bị và nhấn **Save**.
![Tải lên ZIP](/images/5-Workshop/5.7-lambda-functions/upload-zip.png)

**Bước 10:** Chuyển sang thẻ **Configuration**, chọn **Environment variables**. Nhấn **Edit**.

**Bước 11:** Thêm các biến môi trường sau tương ứng với các tài nguyên đã tạo trước đó:

- `COGNITO_USER_POOL_ID`: ID của Cognito User Pool *(Lưu ý: Tạm thời nhập giá trị `temp` hoặc bỏ trống. Bạn sẽ cần quay lại cập nhật biến này sau khi khởi tạo User Pool ở Mục 5.9)*
- `EMAIL_CONFIG_TABLE`: `playwright-email-config`
- `REPORT_BUCKET`: `playwright-report-2026` (hoặc tên bucket duy nhất của bạn)
- `SCHEDULER_ROLE_ARN`: ARN của `playwright-lambda-role`
- `TASK_QUEUE_URL`: URL của `playwright-task-queue`
- `TEST_HISTORY_TABLE`: `playwright-test-history`
- `TEST_SUITES_TABLE`: `playwright-test-suites`
![Biến môi trường API Backend](/images/5-Workshop/5.7-lambda-functions/env-vars-api-backend.png)

---

#### 2. Tạo hàm `playwright-coordinator`

Hàm này lắng nghe hàng đợi và khởi chạy các tác vụ ECS Fargate.

**Bước 1:** Tạo một hàm mới tên là `playwright-coordinator` sử dụng cùng Runtime Python và Role `playwright-lambda-role`.

**Bước 2:** Cuộn xuống phần **Code source**, nhấn **Upload from** và chọn **.zip file**. Tải lên tệp `playwright-coordinator.zip` mà bạn đã chuẩn bị ở phần Chuẩn bị và nhấn **Save**.

**Bước 3:** Tại màn hình tổng quan, nhấn **Add trigger**.

**Bước 4:** Chọn nguồn là **SQS**.

**Bước 5:** Chọn hàng đợi `playwright-task-queue` bạn đã tạo.

**Bước 6:** Đặt **Batch size** là `1`. Điều này đảm bảo mỗi tác vụ ECS được khởi chạy độc lập cho từng yêu cầu. Nhấn **Add**.
![Trigger SQS cho Coordinator](/images/5-Workshop/5.7-lambda-functions/sqs-trigger-coordinator.png)

**Bước 7:** Chuyển sang thẻ **Configuration**, chọn **General configuration** và nhấn **Edit**.

**Bước 8:** Điều chỉnh **Timeout** lên `30` giây và **Ephemeral storage** là `512` MB. Nhấn **Save**.

**Bước 9:** Chuyển sang phần **Environment variables** và thêm các khóa sau với giá trị tương ứng (Subnet, Security Group, tên Cluster...):

- `ASSIGN_PUBLIC_IP`: `DISABLED`
- `CONTAINER_NAME`: `playwright-container`
- `ECS_CLUSTER_NAME`: `playwright-cluster`
- `ECS_TASK_DEFINITION`: `playwright-task-def`
- `SECURITY_GROUP_IDS`: ID của ECS Security Group (VD: `sg-...`)
- `SUBNET_IDS`: ID của Private Subnet (VD: `subnet-...`)
- `TEST_HISTORY_TABLE`: `playwright-test-history`
![Biến môi trường Coordinator](/images/5-Workshop/5.7-lambda-functions/env-vars-coordinator.png)

---

#### 3. Tạo hàm `playwright-error-handler`

Hàm này xử lý các thông điệp bị lỗi trong quá trình điều phối.

**Bước 1:** Tạo một hàm mới tên là `playwright-error-handler` sử dụng cùng Runtime Python và Role `playwright-lambda-role`.

**Bước 2:** Cuộn xuống phần **Code source**, nhấn **Upload from** và chọn **.zip file**. Tải lên tệp `playwright-error-handler.zip` mà bạn đã chuẩn bị ở phần Chuẩn bị và nhấn **Save**.

**Bước 3:** Nhấn **Add trigger**.

**Bước 4:** Chọn **SQS** và trỏ đến `playwright-dlq` (Dead Letter Queue).

**Bước 5:** Đặt **Batch size** là `3`. Nhấn **Add**.
![Trigger SQS cho Error Handler](/images/5-Workshop/5.7-lambda-functions/sqs-trigger-error-handler.png)

**Bước 6:** Chuyển sang phần **Environment variables** và thêm các biến sau:

- `ERROR_DYNAMODB_TABLE`: `playwright-error-log`
- `TEST_HISTORY_TABLE`: `playwright-test-history`
![Biến môi trường Error Handler](/images/5-Workshop/5.7-lambda-functions/env-vars-error-handler.png)

---

#### 4. Tạo hàm `playwright-postprocessing`

Hàm này được kích hoạt bởi EventBridge sau khi tác vụ ECS hoàn tất. Nó xử lý log thực thi, gọi API OpenAI để phân tích kết quả và gửi email thông báo qua SES.

**Bước 1:** Tạo một hàm mới tên là `playwright-postprocessing`.

**Bước 2:** Chọn **Runtime** là `Python 3.14`.

**Bước 3:** Dưới mục **Additional settings**, bật **Custom execution role** và chọn `playwright-postprocessing-role`.

**Bước 4:** Cuộn xuống phần **Code source**, nhấn **Upload from** và chọn **.zip file**. Tải lên tệp `playwright-postprocessing.zip` đã chuẩn bị ở phần Chuẩn bị và nhấn **Save**.

**Bước 5:** Chuyển sang thẻ **Configuration**, chọn **General configuration** và nhấn **Edit**. Đặt **Timeout** thành `2` phút `0` giây. Nhấn **Save**.

**Bước 6:** Chọn **VPC** và nhấn **Edit**. Cấu hình như sau, sau đó nhấn **Save**.

- **VPC**: Chọn `playwright-vpc` của bạn
- **Subnets**: Chọn `playwright-private-subnet`
- **Security groups**: Chọn `playwright-sg-lambda`
![Cấu hình VPC cho Postprocessing](/images/5-Workshop/5.7-lambda-functions/postprocessing-vpc.png)

**Bước 7:** Chọn **Environment variables** và nhấn **Edit**. Thêm các biến sau:

- `EMAIL_CONFIG_TABLE`: `playwright-email-config`
- `LOG_GROUP_NAME`: `/ecs/playwright-runner`
- `OPENAI_SECRET_NAME`: `playwright/openai-api-key`
- `REPORT_BUCKET`: `playwright-report-2026`
- `SES_SENDER_EMAIL`: Địa chỉ email SES của bạn *(Lưu ý: Nhập địa chỉ email bạn dự định sử dụng. Quá trình xác thực email này trên AWS SES sẽ được hướng dẫn chi tiết ở Mục 5.8)*
- `TEST_HISTORY_TABLE`: `playwright-test-history`
![Biến môi trường Postprocessing](/images/5-Workshop/5.7-lambda-functions/postprocessing-env-vars.png)

{{% notice note %}}
**Lưu ý quan trọng về Trigger EventBridge**

Trigger cho hàm `playwright-postprocessing` sẽ được thiết lập từ bảng điều khiển EventBridge ở phần sau. Khi EventBridge gọi hàm Lambda thông qua IAM Role (thay vì resource-based policy), giao diện của AWS Lambda sẽ **không** hiển thị EventBridge trong tab "Triggers". Đây là hạn chế hiển thị của giao diện AWS và không ảnh hưởng đến chức năng của hệ thống.
{{% /notice %}}

---

Bốn hàm Lambda đã được triển khai và cấu hình thành công. Tiếp theo, chúng ta sẽ chuyển sang **[5.8. Secrets và Thông báo](../5.8-secrets-and-notification/)** để thiết lập AWS Secrets Manager lưu trữ API key của OpenAI và cấu hình Amazon SES để gửi email thông báo kết quả kiểm thử.
