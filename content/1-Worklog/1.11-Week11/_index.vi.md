---
title: "Worklog Tuần 11"
date: 2026-06-26
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### Mục tiêu tuần:
Tuần 11 là giai đoạn chốt kiến trúc trước khi triển khai. Trọng tâm của tôi là làm rõ phần Post-processing & AI trong dự án nhóm: Lambda xử lý log sau khi test chạy xong, gọi External AI API để tóm tắt lỗi, lưu báo cáo vào S3, tạo Presigned URL và dùng Secrets Manager để bảo vệ API key.

### Công việc thực hiện trong tuần:
| Thứ | Ngày | Công việc | Nguồn tài liệu |
|---|---|---|---|
| Thứ Sáu | 26/06/2026 | Cùng nhóm rà soát lại kiến trúc tổng thể theo hướng tiết kiệm chi phí hơn; thống nhất bỏ SNS khỏi luồng hiện tại và chuyển phần AI từ AWS Bedrock sang External AI API vì tài khoản thực hành bị giới hạn | Ghi chú họp nhóm |
| Thứ Bảy | 27/06/2026 | Viết lại luồng xử lý sau test cho phần mình phụ trách: CloudWatch lưu log, Lambda post-processing đọc log, lọc thông tin lỗi và chuẩn bị payload gửi sang AI API | docs.aws.amazon.com/cloudwatch, docs.aws.amazon.com/lambda |
| Chủ Nhật | 28/06/2026 | Thiết kế lại dữ liệu cần lưu sau xử lý, gồm trạng thái test, lỗi chính, nội dung AI summary, đường dẫn report HTML/JSON và thời điểm tạo báo cáo | docs.aws.amazon.com/dynamodb |
| Thứ Hai | 29/06/2026 | Tìm hiểu cách lưu report trên S3 và tạo Presigned URL để người dùng xem báo cáo mà không cần public bucket; ghi chú cách phân tách file HTML và JSON cho từng lần test | docs.aws.amazon.com/s3 |
| Thứ Ba | 30/06/2026 | Chốt vị trí của Lambda post-processing trong private subnet; thống nhất Lambda đi qua NAT Gateway khi gọi External AI API, còn truy cập S3/DynamoDB bằng VPC Endpoint để giảm luồng ra internet | docs.aws.amazon.com/vpc |
| Thứ Tư | 01/07/2026 | Nhận phân công chính thức trong nhóm; xác nhận phần việc cá nhân gồm Post-processing & AI, DynamoDB cho lịch sử/log lỗi, S3 report, Secrets Manager, VPC Endpoint và hỗ trợ SES gửi báo cáo | Ghi chú phân công |
| Thứ Năm | 02/07/2026 | Chuẩn bị checklist triển khai: tên bảng DynamoDB, bucket report, secret chứa API key, quyền IAM cho Lambda đọc CloudWatch logs, ghi S3, cập nhật DynamoDB và lấy secret từ Secrets Manager | Boto3 documentation, AWS IAM docs |

### Kết quả đạt được tuần 11:
* Xác định rõ phạm vi phần việc cá nhân trong dự án nhóm là khối Post-processing & AI.
* Hoàn thiện thiết kế luồng Lambda đọc CloudWatch logs, gọi External AI API và tạo dữ liệu báo cáo sau test.
* Chuyển phương án AI từ Bedrock sang External AI API để phù hợp với giới hạn tài khoản thực hành.
* Bổ sung S3 Report và Presigned URL vào kiến trúc để lưu và chia sẻ báo cáo HTML/JSON an toàn hơn.
* Đưa Secrets Manager vào thiết kế để quản lý API key thay vì đặt trực tiếp trong mã nguồn.
* Nắm rõ cách kết hợp private subnet, NAT Gateway và VPC Endpoint cho luồng xử lý vừa cần gọi API bên ngoài vừa cần truy cập dịch vụ AWS nội bộ.
