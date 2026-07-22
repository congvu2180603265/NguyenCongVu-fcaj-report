---
title : "Amazon Cognito"
date : 2026-07-10
weight : 2
chapter : false
pre : " <b> 5.13.2. </b> "
---

#### Xóa Amazon Cognito

1. Truy cập **Amazon Cognito Console** → **User pools**. Chọn user pool `playwright-user-pool`, sau đó nhấn nút **Delete**. Hộp thoại **Delete user pool "playwright-user-pool"?** xuất hiện. Tích chọn cả hai hành động tiên quyết — **Delete Cognito domain `ap-southeast-1dxmd3q6ee` that you assigned** và **Deactivate deletion protection** — rồi nhập `playwright-user-pool` vào ô xác nhận và nhấn **Delete**.
![Cognito 1](/images/5-Workshop/5.13-cleanup/5.13.2-cognito/con1.png?featherlight=false&width=90pc)

2. Sau khi xóa thành công, hệ thống hiển thị banner xanh **"User pool "playwright-user-pool" has been deleted successfully."** và danh sách **User pools (0)** trở về trạng thái **No resources**.
![Cognito 2](/images/5-Workshop/5.13-cleanup/5.13.2-cognito/con2.png?featherlight=false&width=90pc)
