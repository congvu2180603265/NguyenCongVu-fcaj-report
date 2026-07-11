---
title : "Khởi tạo VPC"
date : 2026-07-10
weight : 2
chapter : false
pre : " <b> 5.2.2. </b> "
---

#### 1. Khởi tạo Mạng riêng ảo (VPC)

Trong phần này, chúng ta sẽ tự tay xây dựng hạ tầng mạng từ con số 0. Bước đầu tiên là tạo một không gian mạng riêng ảo (VPC) độc lập.

**Bước 1:** Truy cập vào **VPC Dashboard** trên AWS Console và bấm chọn nút **Create VPC**.

**Bước 2:** Tại giao diện khởi tạo, bạn thiết lập các thông số cơ bản sau:
- **Resources to create:** Chọn **VPC only**.
- **Name tag:** Điền tên cho VPC (ví dụ: `playwright-vpc`).
- **IPv4 CIDR block:** Chọn *IPv4 CIDR manual input* và điền dải mạng `10.0.0.0/16`.

![Cấu hình VPC phần trên](/images/5-Workshop/5.2-Prerequisite/5.2.2-create-vpc/create-vpc-step1.png?featherlight=false&width=90pc)

**Bước 3:** Cuộn xuống phần **Tags** (hệ thống đã tự động điền từ bước trên). Bạn giữ nguyên các cấu hình mặc định còn lại và bấm nút **Create VPC** màu cam.

![Cấu hình VPC phần dưới](/images/5-Workshop/5.2-Prerequisite/5.2.2-create-vpc/create-vpc-step2.png?featherlight=false&width=90pc)

**Bước 4:** Khi thấy màn hình hiện thông báo màu xanh lá, chúc mừng bạn đã khởi tạo thành công khung mạng VPC.

#### 2. Chia các Mạng con (Subnets)

Sau khi có VPC, chúng ta cần chia nó thành các Mạng con (Subnet). Hệ thống yêu cầu ít nhất 1 Public Subnet (để giao tiếp Internet) và 1 Private Subnet (để chạy các tác vụ E2E an toàn).

**Bước 1:** Từ menu bên trái, chọn **Subnets** và bấm nút **Create subnet** ở góc trên bên phải.

**Bước 2:** Tại màn hình cấu hình, chọn VPC vừa tạo (`playwright-vpc`). Ở mục **Subnet settings**, điền các thông tin sau:
- **Subnet name:** `playwright-public-subnet`
- **Availability Zone:** Chọn `Asia Pacific (Singapore) / apse1-az2 (ap-southeast-1a)`
- **IPv4 subnet CIDR block:** Điền `10.0.1.0/24`
- **Tags:** Hệ thống tự động điền Key là `Name` và Value là `playwright-public-subnet`.

![Cấu hình phía trên](/images/5-Workshop/5.2-Prerequisite/5.2.2-create-vpc/create-public-subnet.png?featherlight=false&width=90pc)

**Bước 3:** Cuộn xuống dưới cùng, kiểm tra lại các thông số và bấm nút **Create subnet** màu cam.


**Bước 4: Cấu hình Private Subnet**
Quá trình tạo Private Subnet hoàn toàn tương tự như Public Subnet. Bạn tiếp tục bấm **Create subnet** để tạo mạng con thứ hai với các thông số sau:
- **Subnet name:** `playwright-private-subnet`
- **Availability Zone:** Chọn `Asia Pacific (Singapore) / apse1-az1 (ap-southeast-1b)` (hoặc AZ khác AZ của public subnet)
- **IPv4 subnet CIDR block:** Điền `10.0.2.0/24`

#### 3. Khởi tạo Internet Gateway (IGW)

Để Public Subnet có thể giao tiếp với Internet, chúng ta cần một **Internet Gateway**.

**Bước 1:** Ở menu bên trái, chọn **Internet gateways** và bấm nút **Create internet gateway**.

**Bước 2:** Tại màn hình khởi tạo, nhập tên cho IGW ở mục **Name tag** là `playwright-igw` và bấm nút **Create internet gateway**.

![Tạo Internet Gateway](/images/5-Workshop/5.2-Prerequisite/5.2.2-create-vpc/create-igw.png?featherlight=false&width=90pc)

**Bước 3:** Sau khi tạo xong, trạng thái của IGW đang là **Detached**. Bạn bấm vào nút **Actions** ở góc phải, chọn **Attach to VPC**. 

**Bước 4:** Tại màn hình **Attach to VPC**, click vào ô **Available VPCs** và chọn `vpc-... | playwright-vpc` từ danh sách xổ xuống, sau đó bấm nút **Attach internet gateway**.

![Gắn vào VPC](/images/5-Workshop/5.2-Prerequisite/5.2.2-create-vpc/attach-igw.png?featherlight=false&width=90pc)

#### 4. Cấp phát Elastic IP và Khởi tạo NAT Gateway

Private Subnet không thể trực tiếp truy cập Internet. Để cho phép các tài nguyên trong Private Subnet (như ECS Fargate) tải về các gói tin hoặc giao tiếp ra bên ngoài, chúng ta cần một **NAT Gateway** đặt tại Public Subnet, và NAT Gateway này cần một địa chỉ IP tĩnh (**Elastic IP**).

**Bước 1: Cấp phát Elastic IP**
- Từ menu bên trái, chọn **Elastic IPs** (dưới mục Virtual private cloud) và bấm nút **Allocate Elastic IP address**.
- Tại màn hình cấu hình, giữ nguyên các tùy chọn mặc định:
  - **Network border group:** `ap-southeast-1`
  - **Public IPv4 address pool:** `Amazon's pool of IPv4 addresses`
- Cuộn xuống dưới cùng và bấm nút **Allocate**.

![Cấp phát Elastic IP](/images/5-Workshop/5.2-Prerequisite/5.2.2-create-vpc/allocate-eip.png?featherlight=false&width=90pc)

**Bước 2: Khởi tạo NAT Gateway**
- Trở lại menu bên trái, chọn **NAT gateways** và bấm nút **Create NAT gateway**.
- Tại màn hình **NAT gateway settings**, điền chính xác các thông số sau:
  - **Name:** `playwright-nat`
  - **Availability mode:** Chọn `Zonal`
  - **Subnet:** Click vào ô và chọn Public Subnet bạn đã tạo (ví dụ: `subnet-... (playwright-public-subnet)`). **Lưu ý cực kỳ quan trọng:** Phải chọn Public Subnet, không được chọn Private Subnet.
  - **Connectivity type:** `Public`
  - **Elastic IP allocation ID:** Click vào ô và chọn địa chỉ Elastic IP vừa cấp phát ở Bước 1 (ví dụ: `eipalloc-...`).
- Cuộn xuống dưới và bấm nút **Create NAT gateway** màu cam.

![Khởi tạo NAT Gateway](/images/5-Workshop/5.2-Prerequisite/5.2.2-create-vpc/create-nat-gw.png?featherlight=false&width=90pc)

#### 5. Thiết lập Bảng định tuyến (Route Tables)

Route Table giống như biển chỉ đường, quyết định luồng giao thông mạng sẽ đi đâu. Chúng ta cần cấu hình 2 Route Table: một cho Public Subnet (đi ra Internet) và một cho Private Subnet (đi ra NAT Gateway).

**Phần 1: Khởi tạo và cấu hình Public Route Table**
**Bước 1:** Vẫn ở màn hình **Route tables**, bấm nút **Create route table**.

**Bước 2:** Điền **Name** là `playwright-public-rtb` và chọn VPC `vpc-... (playwright-vpc)`, sau đó bấm nút **Create route table** màu cam.

**Bước 3:** Sau khi tạo xong, tại trang chi tiết của `playwright-public-rtb`, chuyển sang tab **Subnet associations** và bấm **Edit subnet associations**.

**Bước 4:** Tick chọn `playwright-public-subnet` và bấm **Save associations**.

**Bước 5:** Tiếp theo, chuyển sang tab **Routes** và bấm **Edit routes**.

**Bước 6:** Bấm **Add route**:
   - **Destination:** Nhập `0.0.0.0/0`
   - **Target:** Chọn **Internet Gateway** và trỏ tới IGW bạn vừa tạo (ví dụ `igw-...`).

**Bước 7:** Bấm **Save changes**.

![Cấu hình Route ra Internet Gateway](/images/5-Workshop/5.2-Prerequisite/5.2.2-create-vpc/public-route-igw.png?featherlight=false&width=90pc)

**Phần 2: Khởi tạo và cấu hình Private Route Table**
**Bước 1:** Vẫn ở màn hình **Route tables**, bấm nút **Create route table**.

**Bước 2:** Điền **Name** là `playwright-private-rtb` và chọn VPC `vpc-... (playwright-vpc)`, sau đó bấm nút **Create route table** màu cam.

![Tạo Private Route Table](/images/5-Workshop/5.2-Prerequisite/5.2.2-create-vpc/create-private-rtb.png?featherlight=false&width=90pc)

**Bước 3:** Sau khi tạo xong, tại trang chi tiết của `playwright-private-rtb`, chuyển sang tab **Subnet associations** và bấm **Edit subnet associations**.

**Bước 4:** Tick chọn `playwright-private-subnet` và bấm **Save associations**.

![Gắn Private Subnet vào Route Table](/images/5-Workshop/5.2-Prerequisite/5.2.2-create-vpc/associate-private-subnet.png?featherlight=false&width=90pc)

**Bước 5:** Tiếp theo, chuyển sang tab **Routes** và bấm **Edit routes**.

**Bước 6:** Bấm **Add route**:
   - **Destination:** Nhập `0.0.0.0/0`
   - **Target:** Chọn **NAT Gateway** và trỏ tới NAT Gateway bạn vừa tạo (`nat-... (playwright-nat)`).

**Bước 7:** Bấm **Save changes**.

![Cấu hình Route ra NAT Gateway](/images/5-Workshop/5.2-Prerequisite/5.2.2-create-vpc/private-route-nat.png?featherlight=false&width=90pc)

#### 6. Thiết lập Security Groups (Tường lửa)

Để các dịch vụ bên trong VPC có thể giao tiếp với nhau an toàn, chúng ta cần thiết lập Security Group (SG). Ở đây chúng ta cần 3 SGs: cho Lambda, cho Fargate và cho VPC Endpoints.
**Lưu ý quan trọng về thứ tự:** Bạn bắt buộc phải tạo SG cho Lambda và Fargate trước, sau đó mới tạo SG cho Endpoint, vì SG của Endpoint cần tham chiếu đến 2 SGs kia trong rule Inbound.

**Phần 1: Tạo SG cho Lambda và Fargate (Source SGs)**
**Bước 1:** Chọn **Security Groups** ở menu bên trái, bấm **Create security group**.

**Bước 2:** Chúng ta sẽ tạo lần lượt 2 SGs với cấu hình giống hệt nhau (chỉ khác tên):
   - **Lambda SG:**
     - **Security group name:** `playwright-sg-lambda`
     - **Description:** `security group for lambda function`
   - **Fargate SG:** (Làm lại bước Create sau khi tạo xong Lambda SG)
     - **Security group name:** `playwright-sg-fargate`
     - **Description:** `security group for fargate`

**Bước 3:** Ở cả 2 SGs, phần **VPC** bạn chọn `playwright-vpc`.

**Bước 4:** **Inbound rules:** Không cần tạo rule nào (Xóa hết nếu có).

**Bước 5:** **Outbound rules:** Giữ nguyên mặc định là `All traffic` ra `0.0.0.0/0`.

**Bước 6:** Bấm **Create security group**.

![Tạo Security Group cho Lambda](/images/5-Workshop/5.2-Prerequisite/5.2.2-create-vpc/create-lambda-sg.png?featherlight=false&width=90pc)

**Phần 2: Tạo SG cho VPC Endpoints (Target SG)**
**Bước 1:** Tiếp tục bấm **Create security group**.

**Bước 2:** **Security group name:** `playwright-sg-endpoint`

**Bước 3:** **Description:** `securitygroup for endpoints`

**Bước 4:** Chọn VPC `playwright-vpc`.

**Bước 5:** **Inbound rules:** Chúng ta cần cho phép Lambda và Fargate gọi đến Endpoints qua cổng HTTPS. Hãy thêm 2 rules:
   - **Type:** `HTTPS` (Tự động nhảy sang Port 443).
   - **Source:** Chọn `Custom`, click vào ô tìm kiếm và chọn `playwright-sg-lambda`.
   - Bấm **Add rule** để thêm rule thứ hai tương tự, nhưng Source chọn `playwright-sg-fargate`.

![Cấu hình Inbound cho Endpoint SG](/images/5-Workshop/5.2-Prerequisite/5.2.2-create-vpc/endpoint-sg-inbound.png?featherlight=false&width=90pc)

**Bước 6:** **Outbound rules:** Giữ nguyên mặc định là `All traffic` ra `0.0.0.0/0`.

![Cấu hình Outbound mặc định](/images/5-Workshop/5.2-Prerequisite/5.2.2-create-vpc/endpoint-sg-outbound.png?featherlight=false&width=90pc)

**Bước 7:** Cuộn xuống và bấm **Create security group**.

---

Tiếp theo, chúng ta sẽ chuyển sang **[5.2.3. Cấu hình IAM](../5.2.3-configure-iam/)** để phân quyền cho các dịch vụ Lambda và ECS hoạt động bên trong VPC này.

