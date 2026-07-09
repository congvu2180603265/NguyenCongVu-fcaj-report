---
title : "Các bước chuẩn bị"
date : 2024-01-01 
weight : 2
chapter : false
pre : " <b> 5.2. </b> "
---

#### Các bước chuẩn bị trước khi thực hành

Trước khi bắt tay vào triển khai phần lõi của hệ thống Playwright Serverless, chúng ta cần hoàn thiện một số bước chuẩn bị mang tính nền tảng.

Các bước này bao gồm:
1. **Công cụ & API Key:** Tải mã nguồn và lấy khóa kết nối Google Gemini AI.
2. **Khởi tạo VPC:** Xây dựng mạng riêng ảo an toàn cho các container Playwright.
3. **Cấu hình IAM:** Phân quyền chặt chẽ cho các dịch vụ Lambda và ECS.
4. **Khởi tạo S3:** Chuẩn bị kho lưu trữ báo cáo HTML và mã nguồn Frontend.
5. **Khởi tạo ECR:** Nơi chứa các Docker Image của hệ thống.

Hãy bắt đầu với bước đầu tiên ở menu bên trái nhé!