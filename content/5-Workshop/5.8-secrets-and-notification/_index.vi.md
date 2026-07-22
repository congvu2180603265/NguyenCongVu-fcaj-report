---
title : "Bảo mật & Thông báo"
date : 2026-06-19
weight : 8
chapter : false
pre : " <b> 5.8. </b> "
---
#### Giới thiệu

Mục này thiết lập 2 mảng độc lập nhưng cùng phục vụ cho Lambda `playwright-postprocessing`: nơi lưu trữ khóa bí mật an toàn, và kênh gửi thông báo kết quả test ra ngoài. Gồm 2 phần con:

**5.8.1 — Secrets Manager**
Tạo secret `playwright/openai-api-key` trong AWS Secrets Manager để lưu AI API Key. Thay vì hard-code khóa trong source code Lambda (rủi ro lộ thông tin, khó thay đổi), Lambda sẽ tự động truy xuất khóa từ Secrets Manager mỗi khi chạy, thông qua biến môi trường `OPENAI_SECRET_NAME`.

**5.8.2 — Notification (SES)**
Cấu hình Amazon SES để Lambda `playwright-postprocessing` tự động gửi email báo cáo kết quả test sau mỗi lần chạy. Gồm việc verify email người gửi, test gửi thử, và lưu ý quan trọng về giới hạn SES Sandbox mode (chỉ gửi được cho email đã verify) cùng cách xin Production Access khi cần gửi rộng rãi hơn.

Xem chi tiết từng bước thực hiện ở 2 mục con bên dưới.
