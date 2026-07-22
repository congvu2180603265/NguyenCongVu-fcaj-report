---
title : "Amazon ECR"
date : 2026-07-10
weight : 7
chapter : false
pre : " <b> 5.13.7. </b> "
---

#### Xóa Amazon ECR

1. Truy cập **Amazon ECR Console** → **Private registry** → **Repositories**. Trong danh sách **Private repositories (1)**, chọn repository `playwright-runner`, sau đó nhấn nút **Delete**.
![ECR 1](/images/5-Workshop/5.13-cleanup/5.13.7-ecr/ecr1.png?featherlight=false&width=90pc)

2. Hộp thoại **Delete playwright-runner** xuất hiện với nội dung: "Are you sure you want to delete private repository: `playwright-runner`?". Nhập `delete` vào ô xác nhận và nhấn **Delete**.
![ECR 2](/images/5-Workshop/5.13-cleanup/5.13.7-ecr/ecr2.png?featherlight=false&width=90pc)

3. Sau khi xóa thành công, hệ thống hiển thị banner xanh **"Successfully deleted private repository, playwright-runner"** và danh sách **Private repositories** hiển thị **No repositories** — **No repositories were found**.
![ECR 3](/images/5-Workshop/5.13-cleanup/5.13.7-ecr/ecr3.png?featherlight=false&width=90pc)
