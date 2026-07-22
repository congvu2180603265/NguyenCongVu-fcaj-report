---
title : "Amazon DynamoDB"
date : 2026-07-10
weight : 11
chapter : false
pre : " <b> 5.13.11. </b> "
---

#### Xóa Amazon DynamoDB

1. Truy cập **Amazon DynamoDB Console** → **Tables**. Trong danh sách **Tables (4/4)**, chọn tất cả 4 bảng: `playwright-email-config`, `playwright-error-log`, `playwright-test-history`, và `playwright-test-suites`. Sau đó nhấn nút **Delete**.
![DynamoDB 1](/images/5-Workshop/5.13-cleanup/5.13.11-dynamodb/DynamoDB1.png?featherlight=false&width=90pc)

2. Hộp thoại **Delete tables** xuất hiện, cảnh báo rằng việc xóa 4 bảng ở *Asia Pacific (Singapore)* không thể hoàn tác và dữ liệu sẽ không thể khôi phục. Tùy chọn tích chọn **Delete all CloudWatch alarms for the 4 tables selected**. Nhập `confirm` vào ô xác nhận và nhấn **Delete**.
![DynamoDB 2](/images/5-Workshop/5.13-cleanup/5.13.11-dynamodb/DynamoDB2.png?featherlight=false&width=90pc)

3. Hệ thống hiển thị banner xanh: **"The request to delete the 'playwright-email-config, playwright-error-log, playwright-test-history, playwright-test-suites' tables has been submitted successfully."** Danh sách **Tables (4)** hiển thị tất cả 4 bảng với status **Deleting**.
![DynamoDB 3](/images/5-Workshop/5.13-cleanup/5.13.11-dynamodb/DynamoDB3.png?featherlight=false&width=90pc)

4. Sau khi việc xóa hoàn tất, danh sách **Tables (0)** hiển thị **"You have no tables in this account in this AWS Region."**
![DynamoDB 4](/images/5-Workshop/5.13-cleanup/5.13.11-dynamodb/DynamoDB4.png?featherlight=false&width=90pc)
