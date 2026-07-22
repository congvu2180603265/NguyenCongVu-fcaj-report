---
title : "AWS Lambda"
date : 2026-07-10
weight : 4
chapter : false
pre : " <b> 5.13.4. </b> "
---

#### Xóa AWS Lambda

1. Truy cập **Lambda Console** → **Functions**. Chọn tất cả 4 functions có tiền tố `playwright-` (ví dụ: `playwright-lambda-trigger`, `playwright-postprocessing`, `playwright-runner`, `playwright-scheduler`), sau đó chọn **Actions** → **Delete**. Hộp thoại **Delete functions** xuất hiện, xác nhận việc xóa vĩnh viễn 4 functions. Nhập `confirm` vào ô xác nhận và nhấn **Delete**.
![Lambda 1](/images/5-Workshop/5.13-cleanup/5.13.4-lambda/lamda1.png?featherlight=false&width=90pc)

2. Sau khi xóa thành công, hệ thống hiển thị banner xanh **"Successfully deleted 4 functions"** và danh sách **Functions (0)** hiển thị **There is no data to display**.
![Lambda 2](/images/5-Workshop/5.13-cleanup/5.13.4-lambda/lamda2.png?featherlight=false&width=90pc)
