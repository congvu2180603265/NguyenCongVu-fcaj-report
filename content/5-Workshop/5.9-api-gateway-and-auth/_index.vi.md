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

**Bước 2:** Bấm nút cam **Create user pool**.

**Bước 3:** Ở trang **Configure sign-in experience**: mục Provider types giữ **Cognito user pool**; mục Sign-in options tick **Email**. Bấm **Next**.
![Cấu hình sign-in experience với Email](/images/5-Workshop/5.9-api-gateway-and-auth/1.3-cognito-configure-signin.png?featherlight=false&width=90pc)
**Bước 4:** Ở trang **Configure security requirements**: mục Password policy chọn **Cognito defaults**; mục Multi-factor authentication chọn **No MFA**. Bấm **Next**.

**Bước 5:** Ở trang **Configure sign-up experience**: mục Required attributes for sign-up, tick **email**. Các mục còn lại giữ mặc định. Bấm **Next**.

**Bước 6:** Ở trang **Configure message delivery**: giữ mặc định (Send email with Cognito). Bấm **Next**.

**Bước 7:** Ở trang **Integrate your app**: 
- User pool name: điền `playwright-user-pool`
- Mục Initial app client → App type: chọn **Public client**
- App client name: điền `playwright-app`

**Bước 8:** Kéo xuống mục **Hosted authentication pages** (nếu có) hoặc để mặc định, không bật.

**Bước 9:** Ở mục **Callback URL(s)**: điền tạm `http://localhost:3000` (sẽ cập nhật lại thành CloudFront Domain Name sau khi hoàn thành mục 5.10).

**Bước 10:** Bấm **Next**, xem lại toàn bộ cấu hình ở trang **Review and create**, bấm **Create user pool**.

![User Pool playwright-user-pool đã được tạo](/images/5-Workshop/5.9-api-gateway-and-auth/1-user-pool-created.png?featherlight=false&width=90pc)

---

#### Phần 2: Tạo Group

**Bước 1:** Từ trang User Pool vừa tạo (`playwright-user-pool`), nhìn menu bên trái, mở mục **User management**.

**Bước 2:** Bấm vào **Groups**.

**Bước 3:** Bấm nút **Create group**.

**Bước 4:** Điền Group name = `Admin`, các mục khác để trống, bấm **Create group**.
![Group Admin đã được tạo](/images/5-Workshop/5.9-api-gateway-and-auth/2.4-group-admin-created.png?featherlight=false&width=90pc)

**Bước 5:** Bấm **Create group** lần nữa, điền Group name = `Developer`, bấm **Create group**.

**Bước 6:** Bấm **Create group** lần nữa, điền Group name = `QA`, bấm **Create group**.

![3 Group Admin, Developer, QA đã được tạo](/images/5-Workshop/5.9-api-gateway-and-auth/2-groups-created.png?featherlight=false&width=90pc)

**Bước 7:** Vẫn ở menu bên trái, mở mục **Applications** → bấm **App clients**.

**Bước 8:** Xác nhận App Client `playwright-app` đã được tạo sẵn từ Bước 7 ở Phần 1 (Cognito tự tạo khi tạo User Pool). Nếu chưa có, bấm **Create app client** để tạo thêm.

---

#### Phần 3: Tạo tài khoản test

**Bước 1:** Ở menu bên trái, mở mục **User management** → bấm **Users**.

**Bước 2:** Bấm nút **Create user**.

**Bước 3:** Ở mục Invitation message: chọn **Don't send an invitation**.

**Bước 4:** Điền Email address = `Admin-test@gmail.com`, tick **Mark email address as verified** nếu muốn bỏ qua bước xác thực email (không bắt buộc cho tài khoản test nội bộ).
![Điền thông tin tạo user Admin-test](/images/5-Workshop/5.9-api-gateway-and-auth/3.4-create-user-admin-test.png?featherlight=false&width=90pc)
**Bước 5:** Đặt Temporary password (hoặc để Cognito tự sinh), bấm **Create user**.

**Bước 6:** Lặp lại Bước 2-5 để tạo user `Dev-test@gmail.com`.

**Bước 7:** Lặp lại Bước 2-5 để tạo user `Qa-test@gmail.com`.

![3 tài khoản test đã được tạo, trạng thái Enabled](/images/5-Workshop/5.9-api-gateway-and-auth/3-test-users-created.png?featherlight=false&width=90pc)

---

#### Phần 4: Gán tài khoản test vào Group

**Bước 1:** Từ danh sách **Users**, bấm vào user `Admin-test@gmail.com`.

**Bước 2:** Kéo xuống mục **Group memberships**, bấm **Add user to group**.

**Bước 3:** Tick chọn Group **Admin**, bấm **Add**.

![User Admin-test đã được gán vào Group Admin](/images/5-Workshop/5.9-api-gateway-and-auth/4-user-admin-added.png?featherlight=false&width=90pc)

**Bước 4:** Quay lại danh sách **Users**, bấm vào user `Dev-test@gmail.com`.

**Bước 5:** Kéo xuống mục **Group memberships**, bấm **Add user to group**, tick chọn Group **Developer**, bấm **Add**.

![User Dev-test đã được gán vào Group Developer](/images/5-Workshop/5.9-api-gateway-and-auth/5-user-developer-added.png?featherlight=false&width=90pc)

**Bước 6:** Quay lại danh sách **Users**, bấm vào user `Qa-test@gmail.com`.

**Bước 7:** Kéo xuống mục **Group memberships**, bấm **Add user to group**, tick chọn Group **QA**, bấm **Add**.

![User Qa-test đã được gán vào Group QA](/images/5-Workshop/5.9-api-gateway-and-auth/6-user-qa-added.png?featherlight=false&width=90pc)

---

#### Phần 5: Tạo API Gateway
 
**Bước 1:** Tại thanh tìm kiếm AWS Console, gõ `API Gateway`, chọn **API Gateway**.
 
**Bước 2:** Menu bên trái bấm **APIs** → bấm nút cam **Create API**.
 
**Bước 3:** Ở mục **HTTP API**, bấm **Build** (không chọn REST API — HTTP API đơn giản hơn và đủ dùng cho workshop này).
![Chọn HTTP API để Build](/images/5-Workshop/5.9-api-gateway-and-auth/5.3-api-gateway-http-build.png?featherlight=false&width=90pc)
**Bước 4:** Ở trang **Create and configure integrations**: bấm **Add integration** → chọn **Lambda**.
 
**Bước 5:** Ở dropdown Lambda function, chọn `playwright-api-backend`. Đặt **API name** = `playwright-api`. Bấm **Next**.
![Chọn Lambda integration và đặt tên API](/images/5-Workshop/5.9-api-gateway-and-auth/5.5-api-integration-lambda.png?featherlight=false&width=90pc)
**Bước 6:** Ở trang **Configure routes** (optional): Method chọn **POST**, Resource path điền `/trigger`, Integration target giữ nguyên `playwright-api-backend (Lambda)`. Bấm **Next**.
 
**Bước 7:** Ở trang **Define stages** (optional): giữ mặc định Stage name = `$default`, Auto-deploy = **enabled**. Bấm **Next**.
![Cấu hình stage $default với Auto-deploy](/images/5-Workshop/5.9-api-gateway-and-auth/5.7-api-stages-default.png?featherlight=false&width=90pc)
**Bước 8:** Ở trang **Review and create**, kiểm tra lại đúng 3 mục: **API name and integrations** (playwright-api, playwright-api-backend), **Routes** (POST /trigger → playwright-api-backend), **Stages** ($default, Auto-deploy: enabled). Bấm **Create**.
 
![Review and create trước khi tạo API](/images/5-Workshop/5.9-api-gateway-and-auth/7a-review-create-api.png?featherlight=false&width=90pc)
 
**Bước 9:** Xác nhận thông báo **"Successfully created API playwright-api (8bsb7jbhu7)"** — ID API tự sinh sẽ khác của bạn.
 
![API playwright-api đã được tạo thành công](/images/5-Workshop/5.9-api-gateway-and-auth/7b-api-created.png?featherlight=false&width=90pc)
 
---
 
#### Phần 6: Tạo Authorizer và gắn vào Route
 
**Bước 1:** Vào API `playwright-api` vừa tạo, menu bên trái (mục **Develop**) bấm **Authorization**.
 
**Bước 2:** Ở tab **Manage authorizers**, bấm nút **Create**.
 
**Bước 3:** Authorizer type: chọn **JWT**.
 
**Bước 4:** Name: đặt tên `cognito-authorizer`.
 
**Bước 5:** Identity source: điền `$request.header.Authorization`.
 
**Bước 6:** Issuer URL: điền `https://cognito-idp.ap-southeast-1.amazonaws.com/<user-pool-id>` (thay `<user-pool-id>` bằng ID thật, ví dụ `ap-southeast-1_DXMd3Q6ee` — xem ở Cognito Console → User pool → Overview).
 
**Bước 7:** Audience: điền tên hoặc ID App Client, ví dụ `playwright-app`.
 
**Bước 8:** Bấm **Create**, xác nhận thông báo **"Successfully created authorizer 'cognito-authorizer'"**.
 
![Authorizer cognito-authorizer đã được tạo](/images/5-Workshop/5.9-api-gateway-and-auth/8a-authorizer-created.png?featherlight=false&width=90pc)
 
**Bước 9:** Chuyển sang tab **Attach authorizers to routes**.
 
**Bước 10:** Ở khung **Routes for playwright-api**, mở mục `/trigger`, chọn route **POST**.
 
**Bước 11:** Ở khung bên phải **Authorizer for route POST /trigger**, chọn `cognito-authorizer`, xác nhận route hiển thị badge **JWT Auth** màu xanh.
 
![Route POST /trigger đã gắn JWT Auth](/images/5-Workshop/5.9-api-gateway-and-auth/8b-route-jwt-attached.png?featherlight=false&width=90pc)
 
*(Ghi chú: Khung bên phải sẽ hiển thị lại đầy đủ Identity source, Issuer, Audience vừa cấu hình — dùng để kiểm tra nhanh nếu sau này gặp lỗi 401.)*
 
---
 
#### Phần 7: Deploy API và lấy Invoke URL
 
**Bước 1:** Menu bên trái (mục **Deploy**) bấm **Stages**.
 
**Bước 2:** Bấm nút cam **Deploy** ở góc trên phải, chọn stage `$default`, bấm **Deploy**.
 
**Bước 3:** Ở khung **Stage details**, copy **Invoke URL**, ví dụ dạng `https://8bsb7jbhu7.execute-api.ap-southeast-1.amazonaws.com` — dùng cho mục 5.10 (Frontend).
 
![Stage $default với Invoke URL](/images/5-Workshop/5.9-api-gateway-and-auth/9-stage-invoke-url.png?featherlight=false&width=90pc)
 
---
 
#### Kiểm tra
 
- Test thử API bằng Postman hoặc curl kèm JWT token:
```bash
curl -X POST <invoke-url>/trigger -H "Authorization: Bearer <JWT>" -d '{"target_url":"https://example.com"}'
```
→ phải nhận về response đúng, không lỗi 401/403.
- Nếu bị lỗi 401 dù token hợp lệ, kiểm tra lại 2 giá trị ở Authorizer: **Issuer** phải đúng dạng `https://cognito-idp.ap-southeast-1.amazonaws.com/<user-pool-id>`, và **Audience** phải đúng App Client — sai 1 trong 2 sẽ luôn trả 401.
---

Tiếp theo, chúng ta sẽ chuyển sang **[5.10. Frontend](../5.10-frontend/)** để triển khai Dashboard UI lên S3 và CloudFront.
