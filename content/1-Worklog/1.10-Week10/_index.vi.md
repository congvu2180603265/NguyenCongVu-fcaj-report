---
title: "Worklog Tuần 10"
date: 2026-06-19
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### Mục tiêu tuần:
Tuần này tôi tập trung biến ý tưởng dự án nhóm thành kiến trúc rõ ràng hơn, đặc biệt là phần hậu xử lý kết quả kiểm thử. Thay vì chỉ dừng ở việc chạy test, tôi bắt đầu thiết kế luồng xử lý log sau test, gọi AI để tóm tắt lỗi và lưu báo cáo để người dùng có thể truy cập lại.

### Công việc thực hiện trong tuần:
| Thứ | Ngày | Công việc | Nguồn tài liệu |
|---|---|---|---|
| Thứ Sáu | 19/06/2026 | Rà soát lại toàn bộ hướng dự án và thử phác thảo các luồng kiến trúc khác nhau, trong đó tách riêng phần chạy test, lưu log và tạo báo cáo | Ghi chú nhóm |
| Thứ Bảy | 20/06/2026 | Cùng nhóm viết bản mô tả chi tiết hơn cho hệ thống; tôi tập trung mô tả vai trò của khối Post-processing & AI sau khi tác vụ kiểm thử hoàn tất | Ghi chú nhóm |
| Chủ Nhật | 21/06/2026 | Thiết kế lại phần dữ liệu theo hướng phục vụ báo cáo: xác định thông tin cần lưu cho lịch sử test, lỗi phát sinh, nội dung tóm tắt và đường dẫn report | docs.aws.amazon.com/dynamodb |
| Thứ Hai | 22/06/2026 | Làm rõ vị trí của Lambda hậu xử lý trong sơ đồ: Lambda đọc log từ CloudWatch, gom dữ liệu cần thiết, gọi External AI API và chuẩn bị dữ liệu để tạo report | docs.aws.amazon.com/lambda |
| Thứ Ba | 23/06/2026 | Bổ sung luồng lưu report vào S3 dưới dạng HTML/JSON, đồng thời tìm hiểu cách tạo Presigned URL để người dùng mở kết quả mà không cần public toàn bộ bucket | docs.aws.amazon.com/s3 |
| Thứ Tư | 24/06/2026 | Trình bày sơ đồ với mentor cùng nhóm; ghi lại các điểm cần chỉnh về luồng dữ liệu, quyền truy cập và ranh giới giữa Lambda, S3, CloudWatch, DynamoDB và AI API | Ghi chú review |
| Thứ Năm | 25/06/2026 | Tìm hiểu VPC Endpoint theo nhu cầu triển khai: Gateway Endpoint cho S3/DynamoDB, Interface Endpoint cho ECR; đồng thời ghi chú cách Secrets Manager bảo vệ API key dùng để gọi AI API | docs.aws.amazon.com/vpc |

### Kết quả đạt được tuần 10:
* Xác định rõ phần việc cá nhân trong dự án: xây dựng luồng Post-processing & AI cho kết quả kiểm thử.
* Hoàn thiện bản nháp luồng Lambda đọc CloudWatch logs, gọi External AI API để tóm tắt lỗi và chuẩn bị dữ liệu báo cáo.
* Thiết kế hướng lưu báo cáo HTML/JSON trên S3 và dùng Presigned URL để chia sẻ kết quả an toàn hơn.
* Bổ sung Secrets Manager vào kiến trúc để quản lý API key thay vì đặt trực tiếp trong mã nguồn hoặc biến cấu hình không an toàn.
* Hiểu rõ hơn cách VPC Endpoint hỗ trợ truy cập riêng tư đến S3, DynamoDB và ECR trong kiến trúc nhóm.
* Đóng góp vào bản mô tả chi tiết và sơ đồ kiến trúc tổng thể của hệ thống gồm CloudFront, S3, Cognito, API Gateway, SQS, ECS Fargate, ECR, DynamoDB, Lambda, CloudWatch, Secrets Manager, VPC và SES.
