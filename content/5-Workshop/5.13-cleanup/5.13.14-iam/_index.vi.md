---
title : "IAM"
date : 2026-07-10
weight : 14
chapter : false
pre : " <b> 5.13.14. </b> "
---

#### Xóa IAM Roles

Xóa 4 IAM role được tạo riêng cho workshop này.

1. Truy cập **IAM Console** → **Access Management** → **Roles**. Trong danh sách **Roles (4/16)**, chọn 4 role có tiền tố `playwright-`: `playwright-ecs-execution-role`, `playwright-ecs-task-role`, `playwright-lambda-role`, và `playwright-postprocessing-role`. Sau đó nhấn nút **Delete**.
![IAM 1](/images/5-Workshop/5.13-cleanup/5.13.14-iam/IAM1.png?featherlight=false&width=90pc)

2. Hộp thoại **Delete 4 roles?** xuất hiện, xác nhận việc xóa vĩnh viễn 4 role cùng với toàn bộ inline policies và attached instance profiles. Hộp thoại liệt kê: `playwright-lambda-role`, `playwright-ecs-execution-role`, `playwright-ecs-task-role`, và `playwright-postprocessing-role`. Nhập `delete` vào ô xác nhận và nhấn **Delete**.
![IAM 2](/images/5-Workshop/5.13-cleanup/5.13.14-iam/IAM2.png?featherlight=false&width=90pc)

3. Sau khi thành công, hệ thống hiển thị banner xanh **"Roles deleted."** Danh sách **Roles (12)** không còn chứa bất kỳ role nào có tiền tố `playwright-`, xác nhận tất cả IAM role liên quan đến workshop đã được xóa hoàn toàn.
![IAM 3](/images/5-Workshop/5.13-cleanup/5.13.14-iam/IAM3.png?featherlight=false&width=90pc)
