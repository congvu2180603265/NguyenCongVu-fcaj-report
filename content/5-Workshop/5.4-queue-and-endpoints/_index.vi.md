---
title : "Hàng đợi & VPC Endpoints"
date : 2026-07-10
weight : 4
chapter : false
pre : " <b> 5.4. </b> "
---

#### Tạo SQS Queue

**Bước 1:** Truy cập vào **AWS Management Console**, tìm kiếm **Simple Queue Service** và chọn **Simple Queue Service**.

**Bước 2:** Bấm **Create queue**.

![Create Queue](/images/5-Workshop/5.4-queue-and-endpoints/1-create-queue.png?featherlight=false&width=90pc)

**Bước 3:** Chọn loại **Standard**, đặt tên `playwright-dlq`, các mục khác giữ nguyên không thay đổi .

![Tạo Dead Letter Queue](/images/5-Workshop/5.4-queue-and-endpoints/2-create-dlq.png?featherlight=false&width=90pc)

**Bước 4:** Bấm **Create queue** để lưu.

**Bước 5:** Bấm **Create queue** một lần nữa, đặt tên `playwright-task-queue`.

![Tạo playwright task queue](/images/5-Workshop/5.4-queue-and-endpoints/3-create-ptq.png?featherlight=false&width=90pc)

**Bước 6:** Kéo xuống mục **Dead-letter queue**, bật **Enabled**.

**Bước 7:** Chọn queue = `playwright-dlq`, **Maximum receives** = `3`.

![Gắn Dead Letter Queue vào Task Queue](/images/5-Workshop/5.4-queue-and-endpoints/4-attach-dlq.png?featherlight=false&width=90pc)

**Bước 8:** Bấm **Create queue**.

**Bước 9:** Kiểm tra tab **Dead-letter queue** của `playwright-task-queue` hiển thị đúng `playwright-dlq` đã gắn.

#### Tạo Endpoint 1: S3 (Gateway type)

**Bước 1:** Truy cập vào **AWS Management Console**, tìm kiếm **VPC**, chọn **VPC**, sau đó nhấp vào **Endpoints** từ thanh điều hướng bên trái và nhấn **Create endpoint**.

**Bước 2:** Name tag: điền `playwright-endpoint-s3` .

**Bước 3:** Type: giữ nguyên **AWS services** (mặc định đã chọn sẵn).

**Bước 4:** Ở ô tìm kiếm trong khung Services, gõ: `com.amazonaws.ap-southeast-1.s3`.

![Tìm service S3](/images/5-Workshop/5.4-queue-and-endpoints/5-s3-search.png?featherlight=false&width=90pc)

**Bước 5:** Trong bảng kết quả sẽ hiện 2 dòng cùng service name — tick chọn đúng dòng có cột **Type = Gateway** (đừng chọn dòng Interface bên dưới).

**Bước 6:** Kéo xuống mục **Network settings → VPC**: bấm dropdown "Select a VPC", chọn VPC có tên hiển thị là `playwright-vpc`.

**Bước 7:** Sau khi chọn VPC xong, Console sẽ tự hiện thêm mục **"Route tables"** bên dưới (mục này chỉ xuất hiện sau khi đã chọn VPC, không phải bị thiếu giao diện) — tick chọn route table `playwright-private-rtb`. *Bắt buộc — nếu không tick, S3 sẽ không đi qua endpoint.*

**Bước 8:** Phần **Additional settings** (IP address type, DNS record IP type): giữ mặc định, không cần đổi.

**Bước 9:** Phần **Policy**: giữ mặc định (**Full access**).

**Bước 10:** Bấm nút **Create endpoint** màu cam ở góc dưới phải.

![Endpoint S3 đã được tạo](/images/5-Workshop/5.4-queue-and-endpoints/6-s3-created.png?featherlight=false&width=90pc)

---

#### Tạo Endpoint 2: ECR API (Interface type)

*Fargate cần endpoint này để xác thực (auth) khi pull Docker image.*

**Bước 1:** Vào **VPC Console → Endpoints**, bấm **Create endpoint**.

**Bước 2:** Name tag: điền `playwright-endpoint-ecr-api`.

**Bước 3:** Tìm service: `com.amazonaws.ap-southeast-1.ecr.api`.

![Tìm service ECR API](/images/5-Workshop/5.4-queue-and-endpoints/7-ecr-api-search.png?featherlight=false&width=90pc)

**Bước 4:** Type: **Interface**.

**Bước 5:** VPC: Chọn `playwright-vpc`.

**Bước 6:** Subnets: Chọn `playwright-private-subnet`.

**Bước 7:** Security group: Bỏ chọn SG mặc định, tick chọn `playwright-sg-endpoint`.

**Bước 8:** Enable DNS name: giữ tick (bắt buộc, để các service tự động resolve qua endpoint thay vì internet).

**Bước 9:** Bấm **Create endpoint**.

![Endpoint ECR API đã được tạo](/images/5-Workshop/5.4-queue-and-endpoints/8-ecr-api-created.png?featherlight=false&width=90pc)

---

#### Tạo Endpoint 3: ECR DKR (Interface type)

*Fargate cần endpoint này để pull layer image thật sự của Docker image.*

**Bước 1:** Vào **VPC Console → Endpoints**, bấm **Create endpoint**.

**Bước 2:** Name tag: điền `playwright-endpoint-ecr-dkr`.

**Bước 3:** Tìm service: `com.amazonaws.ap-southeast-1.ecr.dkr`.

![Tìm service ECR DKR](/images/5-Workshop/5.4-queue-and-endpoints/9-ecr-dkr-search.png?featherlight=false&width=90pc)

**Bước 4:** Type: **Interface**.

**Bước 5:** VPC: Chọn `playwright-vpc`.

**Bước 6:** Subnets: Chọn `playwright-private-subnet`.

**Bước 7:** Security group: Bỏ chọn SG mặc định, tick chọn `playwright-sg-endpoint`.

**Bước 8:** Enable DNS name: giữ tick.

**Bước 9:** Bấm **Create endpoint**.

![Endpoint ECR DKR đã được tạo](/images/5-Workshop/5.4-queue-and-endpoints/10-ecr-dkr-created.png?featherlight=false&width=90pc)

---

#### Tạo Endpoint 4: CloudWatch Logs (Interface type)

**Bước 1:** Vào **VPC Console → Endpoints**, bấm **Create endpoint**.

**Bước 2:** Name tag: điền `playwright-endpoint-logs`.

**Bước 3:** Tìm service: `com.amazonaws.ap-southeast-1.logs`.

![Tìm service CloudWatch Logs](/images/5-Workshop/5.4-queue-and-endpoints/11-logs-search.png?featherlight=false&width=90pc)

**Bước 4:** Type: **Interface**.

**Bước 5:** VPC: Chọn `playwright-vpc`.

**Bước 6:** Subnets: Chọn `playwright-private-subnet`.

**Bước 7:** Security group: Bỏ chọn SG mặc định, tick chọn `playwright-sg-endpoint`.

**Bước 8:** Enable DNS name: giữ tick.

**Bước 9:** Bấm **Create endpoint**.

![Endpoint CloudWatch Logs đã được tạo](/images/5-Workshop/5.4-queue-and-endpoints/12-logs-created.png?featherlight=false&width=90pc)

---

#### Kiểm tra Private DNS cho các Interface Endpoint

**Bước 1:** Sau khi tạo xong cả 3 Interface Endpoint (ECR API, ECR DKR, CloudWatch Logs), bấm vào từng cái.

**Bước 2:** Mở tab **Details**.

**Bước 3:** Kiểm tra dòng **Private DNS names enabled** = **Yes**. Nếu là No, bấm **Actions → Modify private DNS name** để bật.

![Kiểm tra Private DNS names enabled api ](/images/5-Workshop/5.4-queue-and-endpoints/13-check-private-dns-api.png?featherlight=false&width=90pc )
![Kiểm tra Private DNS names enabled dkr](/images/5-Workshop/5.4-queue-and-endpoints/14-check-private-dns-dkr.png?featherlight=false&width=90pc)
![Kiểm tra Private DNS names enabled blogs](/images/5-Workshop/5.4-queue-and-endpoints/15-check-private-dns-logs.png?featherlight=false&width=90pc)

#### Kiểm tra

**Bước 1:** Vào **VPC Console → Endpoints**, xác nhận danh sách hiển thị đúng **4 endpoint**: `playwright-endpoint-s3`, `playwright-endpoint-ecr-api`, `playwright-endpoint-ecr-dkr`, `playwright-endpoint-logs`.

**Bước 2:** Tất cả đều ở trạng thái **Available**.

![Kiểm tra available](/images/5-Workshop/5.4-queue-and-endpoints/16-available-check.png?featherlight=false&width=90pc)

**Bước 3:** Vào **VPC Console → Route Tables** → chọn `playwright-private-rtb` (`rtb-08b47df38de10ba54`) → tab **Routes**, xác nhận có 1 route mới dạng prefix list `pl-xxxxx` trỏ tới Endpoint S3 vừa tạo.

![Route Table có route tới S3 Endpoint](/images/5-Workshop/5.4-queue-and-endpoints/17-route-table-check.png?featherlight=false&width=90pc)

---

Các bước khởi tạo Hàng đợi & VPC Endpoints đã hoàn tất. Tiếp theo, chúng ta sẽ chuyển sang **[5.5. Docker Image](../5.5-docker-image/)** để xây dựng Docker image chạy Playwright.
