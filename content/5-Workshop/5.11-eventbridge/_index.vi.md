---
title : "EventBridge"
date : 2026-07-10
weight : 11
chapter : false
pre : " <b> 5.11. </b> "
---

#### Cấu hình Amazon EventBridge để kích hoạt Post-Processing

Trong phần này, bạn sẽ cấu hình một EventBridge rule để tự động kích hoạt Lambda function `playwright-postprocessing` khi một ECS task hoàn thành (thành công hoặc thất bại), cho phép hệ thống tự động xử lý báo cáo và phân tích log bằng AI.

{{% notice note %}}
Nội dung phần này sẽ được cập nhật. Hãy đảm bảo Lambda function `playwright-postprocessing` đã được triển khai (phần 5.7) và ECS Cluster đã được cấu hình (phần 5.6) trước khi tiếp tục.
{{% /notice %}}

---

Tiếp theo, chúng ta sẽ chuyển sang **[5.12. Kiểm thử End-to-End](../5.12-end-to-end-test/)** để xác minh toàn bộ luồng hệ thống.
