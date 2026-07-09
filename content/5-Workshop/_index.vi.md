---
title: "Workshop"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---
# Xây dựng hệ thống Serverless Playwright trên AWS

#### Tổng quan

Trong bài workshop này, bạn sẽ học cách xây dựng một hệ thống kiểm thử tự động (end-to-end testing) sử dụng **Playwright**, được đóng gói bằng **Docker** và triển khai trên **AWS Fargate**. Hệ thống sử dụng kiến trúc serverless được điều phối bởi **AWS Lambda**, **SQS** và **EventBridge**.

Bạn sẽ tiến hành triển khai từng bước các thành phần cốt lõi, từ việc cấu hình mạng bảo mật (VPC), thiết lập lưu trữ và hàng đợi, build Docker image, cho đến việc cung cấp API và giao diện Frontend cho người dùng.

#### Nội dung

1. [Tổng quan về workshop](5.1-Workshop-overview/)
2. [Chuẩn bị](5.2-Prerequisite/)
3. [Lưu trữ & Cơ sở dữ liệu](5.3-storage-and-database/)
4. [Hàng đợi & VPC Endpoints](5.4-queue-and-endpoints/)
5. [Docker Image](5.5-docker-image/)
6. [ECS Cluster & Task Definition](5.6-ecs-cluster/)
7. [Lambda Functions](5.7-lambda-functions/)
8. [Bảo mật & Thông báo](5.8-secrets-and-notification/)
9. [Xác thực & API Gateway](5.9-api-gateway-and-auth/)
10. [Giao diện Frontend](5.10-frontend/)
11. [Lập lịch với EventBridge](5.11-eventbridge/)
12. [Kiểm thử End-to-End](5.12-end-to-end-test/)
13. [Dọn dẹp tài nguyên](5.13-cleanup/)
