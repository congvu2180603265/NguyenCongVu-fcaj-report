---
title : "Amazon API Gateway"
date : 2026-07-10
weight : 3
chapter : false
pre : " <b> 5.13.3. </b> "
---

#### Xóa Amazon API Gateway

1. Truy cập **API Gateway Console** → **APIs**. Chọn API `playwright-api`, sau đó nhấn nút **Delete**. Hộp thoại **Delete API** xuất hiện, cảnh báo rằng thao tác này sẽ xóa vĩnh viễn toàn bộ resources và stages, và các API caller sẽ không thể gọi API nữa. Nhập `confirm` vào ô xác nhận và nhấn **Delete**.
![API Gateway 1](/images/5-Workshop/5.13-cleanup/5.13.3-api-gateway/api1.png?featherlight=false&width=90pc)

2. Sau khi xóa thành công, hệ thống hiển thị banner xanh **"Successfully deleted API `playwright-api`."** và danh sách **APIs (0/0)** trở về trạng thái **No API**.
![API Gateway 2](/images/5-Workshop/5.13-cleanup/5.13.3-api-gateway/api2.png?featherlight=false&width=90pc)
