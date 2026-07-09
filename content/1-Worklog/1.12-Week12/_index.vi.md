---
title: "Worklog Tuần 12"
date: 2026-07-03
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
---

### Mục tiêu tuần:
Tuần 12 chuyển từ thiết kế sang triển khai. Tôi tập trung dựng các tài nguyên phục vụ Post-processing & AI: bảng DynamoDB, bucket lưu report, VPC Endpoint, cấu hình secret cho API key, kiểm tra SES và phối hợp nhóm để chạy thử luồng từ test execution đến báo cáo cuối cùng.

### Công việc thực hiện trong tuần:
| Thứ | Ngày | Công việc | Nguồn tài liệu |
|---|---|---|---|
| Thứ Sáu | 03/07/2026 | Cùng nhóm chia nhỏ kế hoạch triển khai, kiểm tra lại VPC, subnet, route table, NAT Gateway và security group để bảo đảm Lambda post-processing có đúng đường truy cập nội bộ và đường gọi API bên ngoài | Ghi chú triển khai |
| Thứ Bảy | 04/07/2026 | Chuẩn bị cấu hình cho phần hậu xử lý: tên bucket report, cấu trúc thư mục lưu HTML/JSON, biến môi trường Lambda, tên secret chứa AI API key và quyền IAM cần cấp | AWS IAM docs |
| Chủ Nhật | 05/07/2026 | Tạo hai bảng DynamoDB cho test history và error log; phối hợp tạo S3 bucket lưu report, SQS task queue và Dead Letter Queue để hoàn thiện các tài nguyên nền cho luồng xử lý | AWS Console, Boto3 |
| Thứ Hai | 06/07/2026 | Tạo VPC Endpoint cho S3 dạng Gateway và ECR dạng Interface; verify email trên SES, gửi thử email kiểm tra và hỗ trợ cấu hình Lambda/EventBridge Scheduler cho luồng chạy tự động | docs.aws.amazon.com/vpc, docs.aws.amazon.com/ses |
| Thứ Ba | 07/07/2026 | Kiểm thử phần dữ liệu sau test: ghi lịch sử chạy vào DynamoDB, lưu lỗi vào error log, kiểm tra trường AI summary và cập nhật trạng thái xử lý theo từng bước | Ghi chú kiểm thử |
| Thứ Tư | 08/07/2026 | Chạy thử luồng end-to-end với nhóm; kiểm tra việc Lambda nhận log, tạo report HTML/JSON, lưu lên S3 và chuẩn bị Presigned URL để trả kết quả cho người dùng | Ghi chú debug |
| Thứ Năm | 09/07/2026 | Tổng hợp phần đóng góp cá nhân cho báo cáo thực tập, ghi lại các lỗi đã gặp khi nối Lambda, DynamoDB, S3, SES và cách điều chỉnh quyền IAM/biến môi trường | Báo cáo thực tập |

### Kết quả đạt được tuần 12:
* Triển khai được các tài nguyên chính cho phần Post-processing & AI của dự án nhóm.
* Tạo và cấu hình hai bảng DynamoDB phục vụ lưu lịch sử test và log lỗi.
* Chuẩn bị bucket S3 cho report HTML/JSON và xác định cách dùng Presigned URL để chia sẻ báo cáo.
* Cấu hình VPC Endpoint cho S3/ECR, giúp các dịch vụ trong private subnet giao tiếp an toàn hơn với tài nguyên AWS.
* Verify email SES và gửi thử thành công để chuẩn bị cho chức năng gửi báo cáo sau khi test hoàn tất.
* Kiểm thử bước đầu luồng hậu xử lý từ log, dữ liệu DynamoDB, report S3 đến thông báo kết quả.
