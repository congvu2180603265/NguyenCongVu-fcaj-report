---
title : "Amazon SQS"
date : 2026-07-10
weight : 8
chapter : false
pre : " <b> 5.13.8. </b> "
---

#### Xóa Amazon SQS

Có 2 queue cần xóa: `playwright-dlq` và `playwright-task-queue`.

1. Truy cập **Amazon SQS Console** → **Queues**. Trong danh sách **Queues (2)**, chọn `playwright-dlq`, sau đó nhấn nút **Delete**. Hộp thoại **Delete queue** xuất hiện, cảnh báo thao tác này không thể hoàn tác. Nhập `confirm` vào ô xác nhận và nhấn **Delete**.
![SQS 1](/images/5-Workshop/5.13-cleanup/5.13.8-sqs/sqs1.png?featherlight=false&width=90pc)

2. Hệ thống hiển thị banner xanh **"Queue playwright-dlq has been deleted successfully."** và danh sách còn lại **Queues (1)** chỉ hiển thị `playwright-task-queue`.
![SQS 2](/images/5-Workshop/5.13-cleanup/5.13.8-sqs/sqs2.png?featherlight=false&width=90pc)

3. Tiếp theo chọn `playwright-task-queue`, nhấn nút **Delete**. Hộp thoại **Delete queue** xuất hiện xác nhận xóa `playwright-task-queue - contains 0 messages`. Nhập `confirm` và nhấn **Delete**.
![SQS 3](/images/5-Workshop/5.13-cleanup/5.13.8-sqs/sqs3.png?featherlight=false&width=90pc)

4. Hệ thống hiển thị banner xanh **"Queue playwright-task-queue has been deleted successfully."** và danh sách **Queues (0)** hiển thị **No queues** — **No queues available**.
![SQS 4](/images/5-Workshop/5.13-cleanup/5.13.8-sqs/sqs4.png?featherlight=false&width=90pc)
