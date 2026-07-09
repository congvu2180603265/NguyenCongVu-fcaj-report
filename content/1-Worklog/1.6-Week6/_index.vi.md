---
title: "Worklog Tuần 6"
date: 2026-05-22
weight: 1
chapter: false
pre: " <b> 1.6. </b> "
---
### Mục tiêu tuần:
Tập trung đào sâu Module 06 về cơ sở dữ liệu, ưu tiên phần RDS và DynamoDB vì đây là mảng thế mạnh của bản thân, nên dành nhiều thời gian để thực hành kỹ hơn các phần còn lại.

### Công việc thực hiện trong tuần:
| Thứ | Ngày | Công việc | Nguồn tài liệu |
|---|---|---|---|
| Thứ Sáu | 22/05/2026 | Xem bài giảng Module 06 và đọc tài liệu chuyên sâu về Amazon RDS, Aurora; ghi chú các engine phổ biến như MySQL, PostgreSQL, Aurora cùng các khái niệm Multi-AZ và Read Replica | docs.aws.amazon.com/rds |
| Thứ Bảy | 23/05/2026 | Thực hành Lab 05 với Amazon RDS: tạo RDS MySQL, cấu hình Security Group để EC2 có thể kết nối, cài Node.js và MySQL client, rồi triển khai ứng dụng Node.js; xử lý lỗi file .env trống và lỗi package mysql không tương thích bằng cách chuyển sang mysql2 để chạy được ứng dụng và kết nối thành công với RDS | awsstudygroup.com (Lab 5) |
| Chủ Nhật | 24/05/2026 | Ôn lại SQL từ cơ bản đến nâng cao, đặc biệt là JOIN, GROUP BY, subquery và index để sử dụng tốt hơn khi thao tác và tối ưu dữ liệu |  |
| Thứ Hai | 25/05/2026 | Tìm hiểu sâu về Amazon DynamoDB, nắm mô hình NoSQL, Partition Key, Sort Key và cách thiết kế bảng sao cho truy vấn hiệu quả, tránh hot partition | docs.aws.amazon.com/dynamodb |
| Thứ Ba | 26/05/2026 | Thực hành Lab 60 về DynamoDB: tạo bảng bằng AWS CLI, thực hiện đầy đủ CRUD, truy vấn bằng Query và Scan, rồi phân biệt rõ hai kiểu truy vấn này | awsstudygroup.com (Lab 60) |
| Thứ Tư | 27/05/2026 | Tiếp tục Lab 60 với Global Secondary Index (GSI) để truy vấn theo thuộc tính ngoài khóa chính, sau đó thao tác lại toàn bộ quy trình bằng Python SDK Boto3 | awsstudygroup.com (Lab 60) |
| Thứ Năm | 28/05/2026 | Tự viết thêm script với Boto3 để tạo bảng, nạp dữ liệu mẫu, truy vấn và xóa item; đây là bước chuẩn bị trực tiếp cho phần DynamoDB mình sẽ phụ trách trong dự án nhóm |  |

### Kết quả đạt được tuần 6:
* Nắm được kiến thức nền về cơ sở dữ liệu trên AWS, phân biệt rõ mô hình quan hệ như RDS/Aurora và NoSQL như DynamoDB.
* Hoàn thành Lab 05: triển khai ứng dụng Node.js kết nối RDS MySQL và tự xử lý được lỗi `.env` cùng lỗi tương thích package bằng cách chuyển sang `mysql2`.
* Hoàn thành Lab 60: hiểu cách thiết kế bảng, làm CRUD, truy vấn bằng Query/Scan và tạo Global Secondary Index trên DynamoDB.
* Thao tác được DynamoDB bằng cả AWS CLI và Python SDK Boto3.
* Củng cố lại SQL từ cơ bản đến nâng cao để hỗ trợ tốt hơn cho phần thực hành dữ liệu.
* Tạo được nền tảng đủ vững cho nhiệm vụ phụ trách DynamoDB trong dự án nhóm.


