---
title : "Docker Image"
date : 2026-07-10
weight : 5
chapter : false
pre : " <b> 5.5. </b> "
---

#### Build và Push Docker Image Playwright lên Amazon ECR

Trong phần này, bạn sẽ build một Docker image chứa kịch bản kiểm thử Playwright và push nó lên repository Amazon ECR (`playwright-runner`) đã tạo ở phần trước.

### 1. Chuẩn bị Dockerfile

Trước tiên, hãy đảm bảo rằng trong thư mục mã nguồn dự án của bạn đã có file `Dockerfile` để đóng gói Playwright. Dưới đây là một ví dụ `Dockerfile` cơ bản:

```dockerfile
# Sử dụng image chính thức của Playwright
FROM mcr.microsoft.com/playwright:v1.44.0-jammy

# Thiết lập thư mục làm việc
WORKDIR /app

# Copy các file cấu hình package (package.json, package-lock.json)
COPY package*.json ./

# Cài đặt dependencies
RUN npm ci

# Copy toàn bộ mã nguồn kiểm thử vào container
COPY . .

# Lệnh mặc định khi chạy container (sẽ chạy test)
ENTRYPOINT ["node", "entrypoint.js"]
```

### 2. Các bước Push Image lên ECR

Để thực hiện các lệnh dưới đây, bạn có thể copy trực tiếp hoặc xem các lệnh này trong AWS Console ECR bằng cách chọn repository `playwright-runner` và click **"View push commands"**.

**Bước 1: Xác thực Docker client với Amazon ECR**

Lấy token xác thực và chuyển nó cho Docker login command:

```bash
aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin 238337501662.dkr.ecr.ap-southeast-1.amazonaws.com
```

![Xác thực ECR thành công](/images/5-Workshop/5.5-docker-image/1-docker-login.png?featherlight=false&width=90pc)


**Bước 2: Build Docker image**

Đứng tại thư mục chứa `Dockerfile` và mã nguồn Playwright, chạy lệnh sau:

```bash
docker build -t playwright-runner .
```

**Bước 3: Tag Docker image**

Sau khi build xong, tiến hành tag (gắn thẻ) image để có thể push lên repository của bạn:

```bash
docker tag playwright-runner:latest 238337501662.dkr.ecr.ap-southeast-1.amazonaws.com/playwright-runner:latest
```

**Bước 4: Push Docker image lên AWS ECR**

Cuối cùng, push image lên ECR repository:

```bash
docker push 238337501662.dkr.ecr.ap-southeast-1.amazonaws.com/playwright-runner:latest
```

![Push image lên ECR thành công](/images/5-Workshop/5.5-docker-image/2-docker-push.png?featherlight=false&width=90pc)


{{% notice tip %}}
**Mẹo:** Đảm bảo bạn đã cài đặt và cấu hình `aws-cli` trên máy với đầy đủ quyền truy cập ECR (`AdministratorAccess` hoặc `AmazonEC2ContainerRegistryFullAccess`) trước khi thực hiện các lệnh trên.
{{% /notice %}}

---

Sau khi push thành công, bạn có thể kiểm tra trên giao diện AWS Console (dịch vụ Amazon ECR) để thấy image mới đã xuất hiện.

![Danh sách Image trong ECR Repository](/images/5-Workshop/5.5-docker-image/3-ecr-repository-images.png?featherlight=false&width=90pc)

Và bạn cũng có thể click vào phần digest của image để xem chi tiết:

![Chi tiết Docker Image trên ECR](/images/5-Workshop/5.5-docker-image/4-ecr-image-details.png?featherlight=false&width=90pc)

Tiếp theo, chúng ta sẽ chuyển sang **[5.6. ECS Cluster & Task Definition](../5.6-ecs-cluster/)** để cấu hình môi trường chạy container cho Playwright.
