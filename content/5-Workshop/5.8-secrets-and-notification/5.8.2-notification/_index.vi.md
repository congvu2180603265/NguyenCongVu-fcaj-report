---
title : "Thông báo qua SES Email"
date : 2026-06-19
weight : 2
chapter : false
pre : " <b> 5.8.2 </b> "
---

#### Phần 1: Verify email người gửi trên SES
 
**Bước 1:** Vào **Amazon SES Console** (nhớ đúng region `ap-southeast-1`).
 
**Bước 2:** Menu bên trái → **Verified identities** → **Create identity**.
 
**Bước 3:** Identity type: chọn **Email address**.
 
**Bước 4:** Nhập email dùng để gửi báo cáo (email này sẽ đóng vai trò `SES_SENDER_EMAIL` trong biến môi trường của Lambda Post-processing).
 
**Bước 5:** Bấm **Create identity**.
 
![Tạo Verified Identity trên SES](/images/5-Workshop/5.8-secrets-and-notification/5.8.2-notification/1-create-identity.png?featherlight=false&width=90pc)
 
**Bước 6:** AWS sẽ gửi 1 email xác nhận đến địa chỉ vừa nhập → mở hộp thư, bấm link **Verify** trong email đó.
 
**Bước 7:** Quay lại SES Console, refresh — status của identity phải chuyển từ **Pending verification** sang **Verified**.
 
![Identity đã chuyển sang Verified](/images/5-Workshop/5.8-secrets-and-notification/5.8.2-notification/2-identity-verified.png?featherlight=false&width=90pc)
 
---
 
#### Phần 2: Test gửi thử 1 email từ SES Console
 
**Bước 1:** Vào **Verified identities**, click vào email vừa verify.
 
**Bước 2:** Bấm tab **Send test email** (hoặc nút **Send test email** ở góc trên).
 
**Bước 3:** Điền From-address: chọn email đã verify.
 
**Bước 4:** Điền Scenario: chọn **Custom** (hoặc để mặc định).
 
**Bước 5:** Điền To-address: một email khác đã verify (vì đang ở Sandbox, xem lưu ý bên dưới).
 
**Bước 6:** Điền Subject/Body: nhập nội dung test bất kỳ, ví dụ "Test SES Playwright System".
 
**Bước 7:** Bấm **Send test email**.
 
![Điền thông tin test email](/images/5-Workshop/5.8-secrets-and-notification/5.8.2-notification/4-Send-test-email.png?featherlight=false&width=90pc)

**Bước 8:** Kiểm tra hộp thư đến của To-address — phải nhận được email.

![Điền thông tin test email](/images/5-Workshop/5.8-secrets-and-notification/5.8.2-notification/5-mail.png?featherlight=false&width=90pc)
 
---
 
{{% notice warning %}}
**Lưu ý quan trọng: SES Sandbox mode**
 
SES mặc định ở chế độ Sandbox, nghĩa là:
- Chỉ gửi được đến các email đã verify (kể cả email test)
- Giới hạn số lượng gửi/ngày rất thấp
Cách xử lý:
- Nếu demo chỉ gửi cho vài người cố định trong nhóm → verify luôn các email đó ở Phần 1 (lặp lại quy trình verify cho từng email nhận báo cáo)
- Nếu cần gửi cho email bất kỳ (production thật) → phải request Production Access:
  1. SES Console → Account dashboard → mục Sending statistics sẽ thấy banner "Your account is in the sandbox"
  2. Bấm **Request production access**
  3. Điền form: mô tả use case (VD: "Hệ thống gửi báo cáo kết quả test tự động nội bộ công ty"), loại mail (Transactional), ước lượng volume gửi/ngày
  4. Submit — AWS thường phản hồi trong 24h. Nên làm bước này sớm, đừng để sát ngày demo mới request.
{{% /notice %}}

---

Tiếp theo, chúng ta sẽ chuyển sang **[5.9. Auth & API Gateway](../../5.9-api-gateway-and-auth/)** để thiết lập xác thực Cognito và lớp API.