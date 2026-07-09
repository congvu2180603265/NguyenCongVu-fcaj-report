---
title : "Giới thiệu"
date : 2024-01-01 
weight : 1
chapter : false
pre : " <b> 5.1. </b> "
---

#### Giới thiệu về AWS Cloud-Native & Serverless Automation

Kiến trúc Cloud-Native hướng sự kiện (Event-Driven Architecture) là mô hình tối ưu cho các hệ thống kiểm thử tự động hiện đại. Bằng cách sử dụng các dịch vụ Serverless trên AWS, hệ thống chỉ khởi tạo tài nguyên khi có yêu cầu và tự động giải phóng sau khi hoàn thành tác vụ, giúp tối ưu chi phí, nâng cao khả năng mở rộng và đảm bảo tính sẵn sàng.

Môi trường kiểm thử được container hóa bằng Docker và Playwright nhằm đảm bảo tính nhất quán giữa các lần thực thi. Các container được triển khai trên Amazon ECS Fargate trong Private Subnet để thực hiện kiểm thử End-to-End, lưu báo cáo trên Amazon S3, ghi log vào Amazon CloudWatch và cập nhật trạng thái trên Amazon DynamoDB. Ngoài ra, hệ thống còn tích hợp Google Gemini (External AI API) để hỗ trợ phân tích log, tóm tắt kết quả kiểm thử và cung cấp gợi ý xử lý lỗi trước khi gửi báo cáo đến người dùng.

#### Tổng quan về workshop

Trong workshop này, bạn sẽ xây dựng và triển khai một nền tảng kiểm thử tự động End-to-End (E2E) theo kiến trúc Cloud-Native trên AWS. Hệ thống được thiết kế theo mô hình nhiều lớp nhằm đảm bảo khả năng mở rộng, tính linh hoạt và dễ dàng bảo trì.

- **“Backend Engine”** tiếp nhận yêu cầu kiểm thử từ Amazon API Gateway hoặc Amazon EventBridge, sau đó chuyển vào Amazon SQS để xử lý bất đồng bộ. AWS Lambda đóng vai trò điều phối, kích hoạt các container Docker chạy Playwright trên Amazon ECS Fargate. Sau khi hoàn thành, báo cáo được lưu trên Amazon S3, log được ghi nhận trên Amazon CloudWatch và tài nguyên được tự động giải phóng theo mô hình Pay-as-you-go nhằm tối ưu chi phí vận hành.

- **“Dashboard Console”** được triển khai trên Amazon S3 kết hợp Amazon CloudFront, cung cấp giao diện quản trị cho phép Admin, QA/Tester và Developer quản lý kịch bản kiểm thử, theo dõi lịch sử thực thi và xem báo cáo kiểm thử. Người dùng được xác thực bằng Amazon Cognito, trong khi Amazon API Gateway và AWS Lambda đảm nhiệm việc xử lý các yêu cầu từ phía giao diện. Toàn bộ dữ liệu và trạng thái kiểm thử được lưu trữ trên Amazon DynamoDB.

- **“AI Support & Notification”** sử dụng AWS Lambda để xử lý và làm sạch dữ liệu log trước khi gửi đến Google Gemini (External AI API) nhằm phân tích, tóm tắt kết quả và hỗ trợ xác định nguyên nhân lỗi. Sau khi hoàn tất, hệ thống tự động gửi email báo cáo thông qua Amazon SES kèm liên kết tải báo cáo trên Amazon S3, đồng thời vẫn đảm bảo gửi báo cáo ngay cả khi dịch vụ AI tạm thời không khả dụng.

![Architecture](/images/5-Workshop/5.1-Workshop-overview/Architecture.png?featherlight=false&width=90pc)
