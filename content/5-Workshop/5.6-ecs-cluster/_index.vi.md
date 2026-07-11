---
title : "ECS Cluster & Task Definition"
date : 2026-07-10
weight : 6
chapter : false
pre : " <b> 5.6. </b> "
---

#### Tạo ECS Cluster và Task Definition cho Playwright Runner

Trong phần này, bạn sẽ tạo một Amazon ECS Cluster và cấu hình Task Definition — nơi xác định Docker image nào được sử dụng, tài nguyên cấp phát bao nhiêu và IAM role nào được áp dụng khi ECS Fargate chạy các tác vụ kiểm thử Playwright.

### 1. Tạo ECS Cluster

Cluster là một nhóm logic các tài nguyên tính toán (trong trường hợp này là Fargate) để chạy các task của bạn.

**Bước 1:** Đăng nhập vào AWS Management Console, tìm kiếm và chọn dịch vụ **Elastic Container Service (ECS)**.

**Bước 2:** Ở thanh menu bên trái, chọn **Clusters**, sau đó nhấn nút **Create cluster**.

**Bước 3:** Cấu hình Cluster:
- **Cluster name**: Nhập `playwright-cluster`.
- Trong phần **Infrastructure**, đảm bảo tùy chọn **Fargate only** (Serverless) đã được chọn. Các thông số khác bạn có thể để mặc định.

![Cấu hình tạo ECS Cluster](/images/5-Workshop/5.6-ecs-cluster/1-create-cluster.png?featherlight=false&width=90pc)

**Bước 4:** Cuộn xuống dưới cùng và nhấn **Create**. 

Sau khi tạo xong, bạn sẽ thấy thông báo tạo thành công và cluster `playwright-cluster` xuất hiện trong danh sách.

![ECS Cluster được tạo thành công](/images/5-Workshop/5.6-ecs-cluster/2-cluster-created.png?featherlight=false&width=90pc)

Khi nhấn vào tên cluster, bạn sẽ thấy giao diện tổng quan của cluster (chưa có services hay tasks nào đang chạy).

![Chi tiết ECS Cluster](/images/5-Workshop/5.6-ecs-cluster/3-cluster-details.png?featherlight=false&width=90pc)

---

### 2. Tạo Task Definition

Task Definition giống như một bản thiết kế (blueprint) cho ứng dụng của bạn. Nó cho ECS biết cách chạy Docker container.

**Bước 1:** Ở thanh menu bên trái của ECS Console, chọn **Task definitions**.

**Bước 2:** Nhấn vào nút **Create new task definition** và chọn **Create new task definition**.

**Bước 3:** Cấu hình Task definition configuration:
- **Task definition family**: Nhập `playwright-task-def`.

**Bước 4:** Cấu hình Infrastructure requirements:
- **Launch type**: Chọn **AWS Fargate**.
- **Operating system/Architecture**: Chọn **Linux/X86_64**.
- **Task size**: 
  - CPU: `1 vCPU`
  - Memory: `2 GB`
- **Task role**: Mở danh sách và chọn role `playwright-ecs-task-role` đã tạo ở phần trước.
- **Task execution role**: Chọn role `playwright-ecs-execution-role` đã tạo ở phần trước.

**Bước 5:** Cấu hình Container - 1:
- **Name**: Nhập `playwright-runner` (hoặc tên tuỳ ý).
- **Image URI**: Dán URI của Docker image bạn đã push lên ECR ở phần 5.5 (Ví dụ: `238337501662.dkr.ecr.ap-southeast-1.amazonaws.com/playwright-runner:latest`).
- **Port mappings**: Có thể xóa bỏ cổng 80 mặc định (Remove) vì task này chỉ chạy script kiểm thử rồi kết thúc, không cần thiết phải lắng nghe kết nối đến (không chạy web server).

**Bước 6:** Cuộn xuống dưới cùng và nhấn **Create**.

Sau khi khởi tạo thành công, bạn sẽ thấy thông báo tạo Task definition `playwright-task-def:1` thành công cùng với các thông số cấu hình như CPU, Memory và các Roles đã được gán chính xác.

![Task Definition tạo thành công](/images/5-Workshop/5.6-ecs-cluster/4-task-def-created.png?featherlight=false&width=90pc)

---

Tiếp theo, chúng ta sẽ chuyển sang **[5.7. Lambda Functions](../5.7-lambda-functions/)** để triển khai lớp điều phối gọi đến ECS Task này.
