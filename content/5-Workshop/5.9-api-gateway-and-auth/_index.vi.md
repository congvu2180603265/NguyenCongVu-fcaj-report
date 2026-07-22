---
title : "Xác thực & API Gateway"
date : 2026-06-20
weight : 9
chapter : false
pre : " <b> 5.9. </b> "
---

#### Giới thiệu

Thiết lập đăng nhập, phân quyền người dùng và cổng API cho Frontend giao tiếp với Backend.

---

#### Phần 1: Tạo Cognito User Pool

**Bước 1:** Đăng nhập **AWS Management Console**, tại thanh tìm kiếm gõ `Cognito`, chọn **Amazon Cognito**.

**Bước 2:** Trên màn hình danh sách **User pools**, bấm nút màu cam **Create user pool**. AWS sẽ chuyển bạn sang trang **Set up resources for your application** với 3 bước.

**Bước 3:** Tại mục **Define your application**:
- **Application type**: Chọn **Single-page application (SPA)**.
- **Name your application**: Nhập `playwright-app`.

![Cấu hình Define your application - chọn SPA và đặt tên playwright-app](/images/5-Workshop/5.9-api-gateway-and-auth/1.1-cognito-define-app.png?featherlight=false&width=90pc)

**Bước 4:** Tại mục **Configure options**:
- **Options for sign-in identifiers**: Tích chọn **Email**.
- **Required attributes for sign-up**: Tích chọn **email**.

**Bước 5:** Tại mục **Add a return URL - optional**:
- **Return URL**: Nhập `http://localhost:3000` (sẽ cập nhật lại thành CloudFront Domain Name sau khi hoàn thành mục 5.10).

![Cấu hình Configure options và Return URL](/images/5-Workshop/5.9-api-gateway-and-auth/1.2-cognito-configure-options.png?featherlight=false&width=90pc)

**Bước 6:** Bấm nút **Create user directory** ở góc dưới để hoàn tất tạo.

![User Pool playwright-user-pool đã được tạo](/images/5-Workshop/5.9-api-gateway-and-auth/1-user-pool-created.png?featherlight=false&width=90pc)

**Bước 7:** Sau khi tạo thành công, click vào tên User Pool `playwright-user-pool` vừa tạo trong danh sách.

**Bước 8:** Tại trang tổng quan của User Pool, hãy copy và ghi lại **User Pool ID** (có định dạng ví dụ: `ap-southeast-1_xxxxxxxxx`).
![Trang Overview của playwright-user-pool, copy User Pool ID](/images/5-Workshop/5.9-api-gateway-and-auth/1.8-user-pool-overview.png?featherlight=false&width=90pc)

**Bước 9:** Vào mục **Applications** → **App clients** ở menu bên trái, click vào `playwright-app`.

**Bước 10:** Copy và ghi lại **Client ID** (chuỗi ký tự ngẫu nhiên).
![Trang chi tiết App client playwright-app, copy Client ID](/images/5-Workshop/5.9-api-gateway-and-auth/1.10-app-client-detail.png?featherlight=false&width=90pc)

{{% notice warning %}}
Đừng quên mở lại thẻ dịch vụ **AWS Lambda**, tìm hàm `playwright-api-backend` (đã tạo ở Mục 5.7). Vào mục **Configuration -> Environment variables**, nhấn **Edit** và dán ID thật vào biến `COGNITO_USER_POOL_ID`. Nếu bỏ qua bước này, API Backend sẽ trả về lỗi xác thực 401.
{{% /notice %}}

---

#### Phần 2: Tạo User (Tài khoản test)

*(Bài 5.10 yêu cầu dùng 3 tài khoản test để đăng nhập)*

**Bước 1:** Quay lại giao diện của User Pool `playwright-user-pool`, chọn tab **Users**.

**Bước 2:** Bấm nút **Create user**.

**Bước 3:** Cấu hình tài khoản thứ nhất (Admin):
- Chọn **Send an email invitation**.
- Điền **Email address** (dùng email thực của bạn để nhận mật khẩu, hoặc email ảo tùy ý).
- Mục **Password**: Chọn **Generate a password** (Tạo mật khẩu tự động) hoặc tự đặt mật khẩu chuẩn.
- Bấm **Create user**.
![Điền thông tin tạo user Admin-test](/images/5-Workshop/5.9-api-gateway-and-auth/3.4-create-user-admin-test.png?featherlight=false&width=90pc)

**Bước 4:** Lặp lại Bước 2 và Bước 3 để tạo thêm 2 tài khoản nữa (ví dụ: QA và Developer). Sau khi tạo xong, bạn sẽ thấy 3 tài khoản ở trạng thái **Force change password** (yêu cầu đổi mật khẩu ở lần đăng nhập đầu).
![3 tài khoản test đã được tạo](/images/5-Workshop/5.9-api-gateway-and-auth/3-test-users-created.png?featherlight=false&width=90pc)

---

#### Phần 3: Tạo API Gateway và Cấu hình Cognito Authorizer

**Bước 1:** Tại thanh tìm kiếm của AWS Console, gõ `API Gateway` và chọn **API Gateway**.

**Bước 2:** Tìm hộp thoại **HTTP API** (thường dùng nhất cho các ứng dụng Serverless/Lambda hiện đại) và bấm **Build**.
![Chọn HTTP API để Build](/images/5-Workshop/5.9-api-gateway-and-auth/5.3-api-gateway-http-build.png?featherlight=false&width=90pc)

**Bước 3:** Đặt tên API là `playwright-api`. Bấm **Next** cho đến màn hình Review rồi bấm **Create**.
![Tao API playwright-api](/images/5-Workshop/5.9-api-gateway-and-auth/5.5-api-integration-lambda.png?featherlight=false&width=90pc)

![API playwright-api đã được tạo thành công](/images/5-Workshop/5.9-api-gateway-and-auth/7b-api-created.png?featherlight=false&width=90pc)

**Bước 4:** Cấu hình CORS (Để Frontend ở domain khác gọi được API):
- Ở menu bên trái, chọn **CORS**.
- Configure **Access-Control-Allow-Origin**: Điền `*` (hoặc domain CloudFront của bạn sau này) rồi bấm **Add**.
- Configure **Access-Control-Allow-Headers**: Điền `*` hoặc các header cần thiết (như `Authorization`, `Content-Type`).
- Configure **Access-Control-Allow-Methods**: Chọn `*` (hoặc GET, POST, PUT, DELETE, OPTIONS).
- Bấm **Save**.

![Cấu hình CORS trong API Gateway](/images/5-Workshop/5.9-api-gateway-and-auth/3.4.CORS.png?featherlight=false&width=90pc)

**Bước 5:** Thêm Routes và Integration (Kết nối Lambda):
- Ở menu bên trái, chọn **Routes** -> Bấm **Create**.
- Thêm các Route tương ứng với Frontend:
  - **Test**: `POST /trigger` · `GET /test-runs` · `GET /test-runs/{id}` · `GET /stats` · `GET /test-suites`
  - **Schedule**: `GET /schedules` · `POST /schedules` · `DELETE /schedules/{id}`
  - **Config & Users**: `GET /email-config` · `POST /email-config` · `GET /users` · `GET /audit-logs`
  - **Report**: `GET /reports` · `GET /AI-insights`
- Chuyển sang menu **Integrations**, chọn từng Route vừa tạo -> Bấm **Attach integration** -> Chọn **AWS Lambda** -> Chọn các hàm Lambda đã tạo ở bài 5.7 tương ứng -> Bấm **Create**.
  
![Danh sách Routes đã tạo trong API Gateway](/images/5-Workshop/5.9-api-gateway-and-auth/3.5.Routes.png?featherlight=false&width=90pc)

**Bước 6:** Cấu hình Cognito Authorizer (Bảo mật API):

**Vào mục Authorization:**
- Ở menu bên trái dưới mục **Develop**, chọn **Authorization**.

**Tạo Authorizer:**
- Nhấp sang tab **Manage authorizers** (ngay bên cạnh tab Attach authorizers to routes) → Nhấp nút **Create**.
- Điền thông tin cấu hình:
  - **Authorizer type**: Chọn `JWT`.
  - **Name**: Điền `cognito-authorizer`.
  - **Identity source**: Điền `$request.header.Authorization` (AWS tự điền sẵn).
  - **Issuer URL**: Nhập `https://cognito-idp.<region>.amazonaws.com/<User_Pool_ID>`
  - **Audience**: Nhấp **Add audience** rồi nhập App Client ID lấy ở Phần 1.
 ![Mục Authorization trong menu bên trái](/images/5-Workshop/5.9-api-gateway-and-auth/2.6%20taoAPIbaomat.png?featherlight=false&width=90pc)

- Nhấp **Create** để hoàn tất.

![Authorizer CognitoAuth đã được tạo](/images/5-Workshop/5.9-api-gateway-and-auth/8a-authorizer-created.png?featherlight=false&width=90pc)

**Gắn Authorizer vào các Routes:**
- Chuyển lại sang tab **Attach authorizers to routes**.
- Chọn các Route cần bảo mật (như `/trigger`, `/test-runs`,...).
- Tại ô **Select authorizer**, chọn `cognito-authorizer` vừa tạo → Nhấp **Attach authorizer**.

![Route đã gắn JWT Authorizer](/images/5-Workshop/5.9-api-gateway-and-auth/8b-route-jwt-attached.png?featherlight=false&width=90pc)

**Bước 7:** Lấy Invoke URL:
- Trở lại trang tổng quan của HTTP API (mục **Stages** -> chọn stage `$default` hoặc `prod`).
- Bạn sẽ thấy một **Invoke URL** có định dạng: `https://xxxxxxxxxx.execute-api.ap-southeast-1.amazonaws.com`.
- Copy URL này lại. Đây chính là `API_BASE_URL` dùng để dán vào file `config.js` ở Phần 1 - Bước 2 của Bài 5.10.
![Stage $default với Invoke URL](/images/5-Workshop/5.9-api-gateway-and-auth/9-stage-invoke-url.png?featherlight=false&width=90pc)

---

#### Tóm tắt kết quả

Lúc này bạn đã nắm trong tay 3 giá trị quan trọng để làm bài 5.10:
- **API_BASE_URL** (Invoke URL)
- **userPoolId** (Cognito User Pool ID)
- **clientId** (Cognito App Client ID)

---

Tiếp theo, chúng ta sẽ chuyển sang **[5.10. Frontend](../5.10-frontend/)** để triển khai Dashboard UI lên S3 và CloudFront.
