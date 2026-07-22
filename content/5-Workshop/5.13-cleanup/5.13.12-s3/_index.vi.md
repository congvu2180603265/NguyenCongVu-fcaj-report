---
title : "Amazon S3"
date : 2026-07-10
weight : 12
chapter : false
pre : " <b> 5.13.12. </b> "
---

#### Xóa Amazon S3

Có 2 S3 bucket cần xóa: `playwright-webui-12` (lưu trữ static website) và `playwright-report-2026` (lưu báo cáo kiểm thử). Mỗi bucket phải được làm rỗng trước khi xóa.

1. Truy cập **Amazon S3 Console** → **Buckets** → **General purpose buckets**. Danh sách hiển thị **General purpose buckets (1/2)** với `playwright-webui-12` được chọn. Nhấn **Empty** trước để làm rỗng nội dung bucket, sau đó nhấn **Delete**.
![S3 1](/images/5-Workshop/5.13-cleanup/5.13.12-s3/s31.png?featherlight=false&width=90pc)

2. Trên trang **Delete bucket** của `playwright-webui-12`, xem lại các cảnh báo (tên bucket là duy nhất, bucket này được cấu hình để lưu trữ static website). Nhập `playwright-webui-12` vào ô xác nhận và nhấn **Delete bucket**.
![S3 2](/images/5-Workshop/5.13-cleanup/5.13.12-s3/s32.png?featherlight=false&width=90pc)

3. Hệ thống hiển thị banner xanh **"Successfully deleted bucket 'playwright-webui-12'"** và danh sách còn lại **General purpose buckets (1)** chỉ hiển thị `playwright-report-2026`.
![S3 3](/images/5-Workshop/5.13-cleanup/5.13.12-s3/s33.png?featherlight=false&width=90pc)

4. Lặp lại các bước empty và delete cho `playwright-report-2026`. Sau khi thành công, hệ thống hiển thị **"Successfully deleted bucket 'playwright-report-2026'"** và danh sách **General purpose buckets (0)** hiển thị **No buckets** — **You don't have any buckets**.
![S3 4](/images/5-Workshop/5.13-cleanup/5.13.12-s3/s34.png?featherlight=false&width=90pc)
