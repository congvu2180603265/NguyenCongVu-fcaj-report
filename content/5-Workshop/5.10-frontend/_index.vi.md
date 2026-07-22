---
title : "Giao diện Frontend"
date : 2026-06-21
weight : 10
chapter : false
pre : " <b> 5.10. </b> "
---

> **Lưu ý**: Nếu không dùng CloudFront + S3 Web UI thì có thể **bỏ qua toàn bộ mục này**. Nếu frontend host ở nơi khác (Vercel, Amplify, hoặc chạy local để demo), chỉ cần đảm bảo đã cập nhật đúng Invoke URL (mục 5.9) và Cognito App Client ID/Callback URL khớp domain thực tế đang dùng.

#### Giới thiệu

Đưa giao diện Dashboard lên S3 và phân phối qua CloudFront.

---

#### Phần 1: Cập nhật cấu hình và build Frontend

**Bước 1:** Mở source code React (Dashboard, đăng nhập, lịch sử test, xem báo cáo) trong VS Code (hoặc IDE tương đương).

**Bước 2:** Mở file cấu hình (ví dụ `src/config.js`), cập nhật:

```js
// ===== CẤU HÌNH AWS =====
// Cập nhật các giá trị này sau khi deploy lên AWS

export const API_BASE_URL = 'https://8bsb7jbhu7.execute-api.ap-southeast-1.amazonaws.com'

export const COGNITO_CONFIG = {
  region: 'ap-southeast-1',
  userPoolId: 'ap-southeast-1_DXMd3Q6ee',
  clientId: '492jkd32vd241c2tuv1v160g4o',
}
```

Trong đó `API_BASE_URL` là **Invoke URL** lấy ở mục 5.9, `userPoolId` và `clientId` lấy ở Cognito Console → User pool → App integration.

**Bước 3:** Kiểm tra các endpoint API bên dưới đã tự ghép đúng từ `API_BASE_URL`:

```js
// Các endpoint API
export const API_ENDPOINTS = {

  trigger:    `${API_BASE_URL}/trigger`,
  testRuns:   `${API_BASE_URL}/test-runs`, 
  stats:      `${API_BASE_URL}/stats`,
  testSuites: `${API_BASE_URL}/test-suites`,
  schedules:  `${API_BASE_URL}/schedules`,
  emailConfig:`${API_BASE_URL}/email-config`,

  users:      `${API_BASE_URL}/users`,
  auditLogs:  `${API_BASE_URL}/audit-logs`,

  reports:         `${API_BASE_URL}/reports`,
  chatgptInsights: `${API_BASE_URL}/AI-insights`,
}
```

![Cấu hình config.js đã cập nhật Invoke URL và Cognito](/images/5-Workshop/5.10-frontend/1-config-js-updated.png?featherlight=false&width=90pc)

**Bước 4:** Mở Terminal trong VS Code (hoặc terminal riêng), `cd` vào thư mục project Frontend, chạy lệnh:

```bash
npm run build
```

**Bước 5:** Chờ đến khi thấy dòng `✓ built in ...ms`, xác nhận thư mục `dist` được tạo ra với các file (`index.html`, `assets/index-xxxx.css`, `assets/index-xxxx.js`...), sẵn sàng upload lên S3.

![Build thành công, tạo thư mục dist](/images/5-Workshop/5.10-frontend/2-npm-build-success.png?featherlight=false&width=90pc)

---

#### Phần 2: Upload lên S3

**Bước 1:** Vào **S3 Console** → mở bucket `playwright-webui-xxx`.

**Bước 2:** Bấm nút **Upload**.

**Bước 3:** Bấm **Add files** để thêm `favicon.svg`, `icons.svg`, `index.html`; bấm **Add folder** để thêm folder `assets/` (chứa file `.css` và `.js` đã build). Xác nhận khung **Files and folders** hiển thị đủ **5 total** file (`index-xxxx.css`, `index-xxxx.js`, `favicon.svg`, `icons.svg`, `index.html`).

![Danh sách file sẵn sàng upload lên S3](/images/5-Workshop/5.10-frontend/3-s3-upload-file-list.png?featherlight=false&width=90pc)

**Bước 4:** Kéo xuống cuối trang, bấm **Upload**, chờ tới khi hiện thông báo **"Upload succeeded"**.

---

#### Phần 3: Cấu hình Block Public Access

**Bước 1:** Vào bucket `playwright-webui-xxx` → tab **Permissions**.

**Bước 2:** Kéo xuống mục **Block public access (bucket settings)**, bấm **Edit**.

![Mục Block public access trong tab Permissions](/images/5-Workshop/5.10-frontend/3.2-s3-block-public-access.png?featherlight=false&width=90pc)

**Bước 3:** Giữ nguyên **tất cả 4 mục đều tick** (Block all public access = ON) — vì sẽ chỉ cho CloudFront truy cập qua Origin Access Control, không truy cập trực tiếp bucket.

![Tick chọn Block all public access](/images/5-Workshop/5.10-frontend/3.3-s3-edit-block-public-access.png?featherlight=false&width=90pc)

**Bước 4:** Bấm **Save changes**, gõ `confirm` vào ô xác nhận, bấm **Confirm**.

---

#### Phần 4: Tạo CloudFront Distribution

**Bước 1:** Tại thanh tìm kiếm AWS Console, gõ `CloudFront` → Chọn **CloudFront** → Bấm nút **Create distribution**. Ở **Step 1: Choose a plan**, đảm bảo đã tích chọn gói **Free ($0/month)** (mặc định đã được chọn sẵn) → Cuộn xuống góc dưới bên phải và bấm **Next**.

![Chọn gói Free](/images/5-Workshop/5.10-frontend/4.2-cloudfront-choose-plan.png?featherlight=false&width=90pc)

**Bước 2 (Get started):** Tại mục **Distribution options**, điền tên phân phối vào ô **Distribution name** (ví dụ: `playwright-cloudfront-123`), sau đó cuộn xuống cuối trang và bấm **Next**.

![Mở CloudFront Console](/images/5-Workshop/5.10-frontend/4.1-cloudfront-console.png?featherlight=false&width=90pc)

**Bước 3 (Specify origin):** Đây chính là bước cấu hình S3 Bucket:
- **Origin domain**: Bấm vào ô tìm kiếm và chọn bucket `playwright-webui-xxx.s3.ap-southeast-1.amazonaws.com`.
- **Origin access**: Chọn **Origin access control settings (recommended)** → Bấm **Create new OAC** → Giữ nguyên mặc định và bấm **Create**.

![Chọn Origin domain và thiết lập Origin access](/images/5-Workshop/5.10-frontend/4.3-cloudfront-specify-origin.png?featherlight=false&width=90pc)

**Bước 4 (Settings):** Cuộn xuống phần **Settings**, tích chọn **Allow private S3 bucket access to CloudFront**, giữ nguyên các thiết lập khuyên dùng (Recommended) bên dưới và bấm **Next**.

![Thiết lập Origin Settings](/images/5-Workshop/5.10-frontend/4.4-cloudfront-origin-settings.png?featherlight=false&width=90pc)

**Bước 5:** Bấm **Next** qua các bước còn lại → Đến bước cuối cùng (Review and create) bấm **Create distribution**.

![Review and create](/images/5-Workshop/5.10-frontend/4.5-cloudfront-review-create.png?featherlight=false&width=90pc)

**Bước 6 (Cấu hình Default Root Object):** Sau khi tạo xong, ở trang tổng quan Distribution, cuộn xuống mục **Settings** → Nhấn **Edit**.

![Trang tổng quan Distribution, cuộn xuống mục Settings](/images/5-Workshop/5.10-frontend/4.6.1.png?featherlight=false&width=90pc)

Tại ô **Default root object**, nhập `index.html`. Kéo xuống cuối trang → Nhấn **Save changes**.

![Nhập index.html vào Default root object và Save changes](/images/5-Workshop/5.10-frontend/4.6.2.png?featherlight=false&width=90pc)

**Bước 7 (Cập nhật S3 Bucket Policy):** CloudFront sẽ hiển thị banner màu vàng **"The S3 bucket policy needs to be updated"** → Bấm **Copy policy** → Mở tab mới, vào **S3 Bucket** (`playwright-webui-xxx`) → Tab **Permissions** → Mục **Bucket policy** bấm **Edit** → Dán (Paste) đoạn policy vừa copy → Bấm **Save changes**.

> 💡 **Chú thích:** Thao tác này giúp phân quyền cho CloudFront có thể đọc được nội dung trên S3, trong khi người dùng ngoài không thể truy cập trực tiếp vào S3 qua đường link công khai (bảo mật chuẩn AWS).

![Paste policy vào S3 Bucket Policy](/images/5-Workshop/5.10-frontend/4.9-s3-bucket-policy-updated.png?featherlight=false&width=90pc)

**Bước 8 (Cập nhật Callback URL):** Quay lại **Cognito Console** → User pool → **App integration** → App client → Bấm **Edit** → Cập nhật **Callback URL(s)** thành CloudFront Domain Name (dạng `https://dxxxx.cloudfront.net`) → Bấm **Save changes**.

> 💡 **Chú thích:** Cần thay thế địa chỉ `http://localhost:3000` cũ bằng CloudFront Domain để sau khi đăng nhập xong, Cognito biết chuyển hướng (redirect) người dùng về đúng giao diện web thực tế.

![Cập nhật Callback URL trong Cognito App Client](/images/5-Workshop/5.10-frontend/4.10-cognito-callback-url-updated.png?featherlight=false&width=90pc)

---

#### Phần 5: Kiểm tra và hoàn tất

**Bước 1:** Quay lại **CloudFront Console**, chờ cột **Status** chuyển từ **Deploying** sang **Enabled** (khoảng 3 – 5 phút).

![Distribution đang Deploying, chờ sang Enabled](/images/5-Workshop/5.10-frontend/4.7-cloudfront-deploying.png?featherlight=false&width=90pc)

**Bước 2:** Copy **Domain Name** (dạng `dxxxxxxxxxxxxx.cloudfront.net`) và mở trên trình duyệt (nên dùng tab ẩn danh) để xác nhận giao diện hiển thị đúng.

![Dashboard hiển thị đúng giao diện đăng nhập](/images/5-Workshop/5.10-frontend/5.4-cloudfront-dashboard-login.png?featherlight=false&width=90pc)

- Truy cập trực tiếp URL S3 bị chặn (trả lỗi **403 Forbidden**), phải qua CloudFront mới xem được.

![Truy cập trực tiếp S3 bị chặn 403 Forbidden](/images/5-Workshop/5.10-frontend/5.5-s3-direct-403-forbidden.png?featherlight=false&width=90pc)

- Đăng nhập thử bằng 1 trong 3 tài khoản test (Admin/QA/Developer) đã tạo ở mục 5.9, xác nhận đăng nhập thành công và gọi được API (không lỗi CORS/401).

> 💡 **Chú thích:** Nếu sửa giao diện Frontend và upload lại lên S3 sau này mà không thấy web cập nhật, hãy vào tab **Invalidations** trong CloudFront → **Create invalidation** với đường dẫn `/*` để xóa cache cũ.

---

Tiếp theo, chúng ta sẽ chuyển sang **[5.11. EventBridge](../5.11-eventbridge/)** để cấu hình trigger xử lý báo cáo tự động.
