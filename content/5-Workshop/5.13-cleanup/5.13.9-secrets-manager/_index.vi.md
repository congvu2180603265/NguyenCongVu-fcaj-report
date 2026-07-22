---
title : "AWS Secrets Manager"
date : 2026-07-10
weight : 9
chapter : false
pre : " <b> 5.13.9. </b> "
---

#### Xóa AWS Secrets Manager

1. Truy cập **AWS Secrets Manager Console** → **Secrets**. Trong danh sách, nhấn vào secret `playwright/openai-api-key` để mở trang chi tiết.
![Secrets Manager 1](/images/5-Workshop/5.13-cleanup/5.13.9-secrets-manager/secrets-manager1.png?featherlight=false&width=90pc)

2. Trên trang chi tiết của secret `playwright/openai-api-key`, nhấn **Actions** → **Delete secret**. Hộp thoại **Disable secret and schedule deletion** xuất hiện, thông báo rằng Secrets Manager yêu cầu thời gian chờ tối thiểu 7 ngày trước khi xóa secret. Đặt **Waiting period** là `7` ngày và nhấn **Schedule deletion**.
![Secrets Manager 2](/images/5-Workshop/5.13-cleanup/5.13.9-secrets-manager/secrets-manager2.png?featherlight=false&width=90pc)

3. Trang chi tiết của secret hiển thị banner thông báo: **"This secret has been scheduled for deletion."** Panel **Secret details** hiển thị **Deleted on: 7/15/2026** xác nhận việc xóa đã được lên lịch.
![Secrets Manager 3](/images/5-Workshop/5.13-cleanup/5.13.9-secrets-manager/secrets-manager3.png?featherlight=false&width=90pc)
