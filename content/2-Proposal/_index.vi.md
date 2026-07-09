---
title: "Bản đề xuất"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# IoT Weather Platform for Lab Research
## Giải pháp AWS Serverless hợp nhất cho giám sát thời tiết thời gian thực

### 1. Tóm tắt điều hành

IoT Weather Platform được thiết kế cho nhóm ITea Lab tại TP. Hồ Chí Minh nhằm cải thiện việc thu thập, lưu trữ và phân tích dữ liệu thời tiết. Hệ thống hỗ trợ ban đầu 5 trạm thời tiết và có thể mở rộng lên 10-15 trạm, sử dụng Raspberry Pi làm thiết bị biên kết hợp cảm biến ESP32 để gửi dữ liệu qua MQTT.

Nền tảng tận dụng các dịch vụ AWS Serverless để cung cấp giám sát thời gian thực, phân tích xu hướng và tối ưu chi phí vận hành. Quyền truy cập được giới hạn cho 5 thành viên phòng lab thông qua Amazon Cognito.

### 2. Tuyên bố vấn đề

#### Vấn đề hiện tại

Các trạm thời tiết hiện tại yêu cầu thu thập dữ liệu thủ công, gây khó khăn khi số lượng trạm tăng lên. Dữ liệu chưa được tập trung, khó theo dõi theo thời gian thực và chưa có nền tảng phân tích thống nhất. Các nền tảng bên thứ ba như ThingsBoard hoặc CoreIoT có nhiều tính năng nhưng có thể phức tạp và tốn chi phí hơn nhu cầu nội bộ của phòng lab.

#### Giải pháp đề xuất

Nền tảng sử dụng AWS IoT Core để tiếp nhận dữ liệu MQTT từ các trạm thời tiết. Dữ liệu thô được lưu vào Amazon S3 như một data lake, sau đó AWS Glue Crawlers và ETL jobs lập chỉ mục, chuyển đổi và tải dữ liệu sang một S3 bucket khác phục vụ phân tích. AWS Lambda và Amazon API Gateway hỗ trợ xử lý và giao tiếp với ứng dụng web. Giao diện được xây dựng bằng Next.js và triển khai qua AWS Amplify, trong khi Amazon Cognito đảm nhiệm xác thực người dùng.

Giải pháp vẫn hỗ trợ các chức năng quan trọng như đăng ký thiết bị, quản lý kết nối, dashboard thời gian thực và phân tích xu hướng, nhưng được thiết kế ở quy mô nhỏ hơn để phù hợp với mục tiêu nghiên cứu nội bộ.

#### Lợi ích và hoàn vốn đầu tư

Giải pháp giúp giảm thao tác thu thập và tổng hợp dữ liệu thủ công, cải thiện độ tin cậy dữ liệu và tạo nền tảng cho các nghiên cứu AI trong tương lai. Dữ liệu thời tiết được lưu trữ có cấu trúc có thể dùng để huấn luyện mô hình, phân tích xu hướng hoặc phát triển các tính năng dự đoán.

Chi phí vận hành AWS ước tính khoảng 0,70 USD/tháng, tương đương 8,40 USD trong 12 tháng. Chi phí phần cứng dự kiến 265 USD một lần cho Raspberry Pi 5 và cảm biến. Thời gian hoàn vốn kỳ vọng trong khoảng 6-12 tháng nhờ tiết kiệm thời gian vận hành và giảm phụ thuộc vào xử lý thủ công.

### 3. Kiến trúc giải pháp

Nền tảng áp dụng kiến trúc AWS Serverless để quản lý dữ liệu từ các trạm thời tiết dựa trên Raspberry Pi. Dữ liệu được gửi qua MQTT đến AWS IoT Core, lưu vào S3 data lake, sau đó được xử lý bằng AWS Glue để phục vụ phân tích. Lambda và API Gateway hỗ trợ các API cần thiết, còn Amplify triển khai dashboard Next.js được bảo vệ bởi Cognito.

![IoT Weather Station Architecture](/images/proposal-edge-architecture.jpeg)

![IoT Weather Platform Architecture](/images/proposal-platform-architecture.jpeg)

### Dịch vụ AWS sử dụng

- **AWS IoT Core**: Tiếp nhận dữ liệu MQTT từ 5 trạm, có thể mở rộng lên 15 trạm.
- **AWS Lambda**: Xử lý dữ liệu và kích hoạt Glue jobs.
- **Amazon API Gateway**: Cung cấp API cho ứng dụng web.
- **Amazon S3**: Lưu dữ liệu thô trong data lake và dữ liệu đã xử lý trong bucket riêng.
- **AWS Glue**: Crawlers lập chỉ mục dữ liệu; ETL jobs chuyển đổi và tải dữ liệu.
- **AWS Amplify**: Triển khai giao diện web Next.js.
- **Amazon Cognito**: Quản lý xác thực và giới hạn quyền truy cập cho người dùng phòng lab.

### Thiết kế thành phần

- **Thiết bị biên**: Raspberry Pi thu thập, lọc dữ liệu cảm biến và gửi dữ liệu đến IoT Core.
- **Tiếp nhận dữ liệu**: AWS IoT Core nhận tin nhắn MQTT từ các thiết bị biên.
- **Lưu trữ dữ liệu**: Dữ liệu thô lưu trong S3 data lake; dữ liệu đã xử lý lưu ở một S3 bucket khác.
- **Xử lý dữ liệu**: AWS Glue Crawlers lập chỉ mục dữ liệu; ETL jobs chuyển đổi dữ liệu phục vụ phân tích.
- **Giao diện web**: AWS Amplify triển khai ứng dụng Next.js cho dashboard và phân tích thời gian thực.
- **Quản lý người dùng**: Amazon Cognito quản lý tối đa 5 tài khoản hoạt động.

### 4. Triển khai kỹ thuật

#### Các giai đoạn triển khai

Dự án gồm hai phần chính: thiết lập trạm thời tiết biên và xây dựng nền tảng thời tiết trên AWS.

- **Giai đoạn 1 - Nghiên cứu và vẽ kiến trúc**: Nghiên cứu Raspberry Pi, ESP32, cảm biến thời tiết và thiết kế kiến trúc AWS Serverless.
- **Giai đoạn 2 - Ước tính chi phí và kiểm tra tính khả thi**: Sử dụng AWS Pricing Calculator để ước tính chi phí và điều chỉnh phạm vi.
- **Giai đoạn 3 - Tối ưu kiến trúc**: Điều chỉnh thiết kế để phù hợp chi phí, nhu cầu sử dụng và khả năng triển khai.
- **Giai đoạn 4 - Phát triển, kiểm thử và triển khai**: Lập trình thiết bị biên, cấu hình dịch vụ AWS bằng CDK/SDK và xây dựng ứng dụng Next.js.

#### Yêu cầu kỹ thuật

- **Trạm thời tiết biên**: Cảm biến nhiệt độ, độ ẩm, lượng mưa, tốc độ gió; ESP32; Raspberry Pi chạy Raspbian và Docker để lọc dữ liệu trước khi gửi qua MQTT.
- **Nền tảng thời tiết**: AWS Amplify, Lambda, AWS Glue, S3, IoT Core, API Gateway và Cognito.
- **Công cụ phát triển**: AWS CDK/SDK cho hạ tầng và tích hợp dịch vụ; Next.js cho ứng dụng web fullstack.

### 5. Lộ trình và mốc triển khai

- **Trước thực tập (Tháng 0)**: Lập kế hoạch, đánh giá trạm thời tiết cũ và chuẩn bị kiến trúc ban đầu.
- **Tháng 1**: Học các dịch vụ AWS liên quan và nâng cấp phần cứng.
- **Tháng 2**: Thiết kế, điều chỉnh kiến trúc và kiểm tra chi phí.
- **Tháng 3**: Triển khai, kiểm thử và đưa hệ thống vào sử dụng.
- **Sau triển khai**: Duy trì dữ liệu trong tối đa 1 năm để phục vụ nghiên cứu.

### 6. Ước tính ngân sách

Có thể xem chi phí trên [AWS Pricing Calculator](https://calculator.aws/#/estimate?id=621f38b12a1ef026842ba2ddfe46ff936ed4ab01).  
Hoặc tải [tệp ước tính ngân sách](../attachments/budget_estimation.pdf).

### Chi phí hạ tầng

- AWS Lambda: 0,00 USD/tháng (1.000 request, 512 MB storage).
- S3 Standard: 0,15 USD/tháng (6 GB, 2.100 request, 1 GB scanned).
- Data Transfer: 0,02 USD/tháng (1 GB inbound, 1 GB outbound).
- AWS Amplify: 0,35 USD/tháng (256 MB, 500 ms requests).
- Amazon API Gateway: 0,01 USD/tháng (2.000 request).
- AWS Glue ETL Jobs: 0,02 USD/tháng (2 DPUs).
- AWS Glue Crawlers: 0,07 USD/tháng (1 crawler).
- MQTT (IoT Core): 0,08 USD/tháng (5 thiết bị, 45.000 messages).

**Tổng chi phí AWS**: 0,70 USD/tháng, tương đương 8,40 USD trong 12 tháng.  
**Chi phí phần cứng**: 265 USD một lần cho Raspberry Pi 5 và cảm biến.

### 7. Đánh giá rủi ro

#### Ma trận rủi ro

- **Mất kết nối mạng**: Ảnh hưởng trung bình, xác suất trung bình.
- **Hỏng cảm biến**: Ảnh hưởng cao, xác suất thấp.
- **Vượt ngân sách**: Ảnh hưởng trung bình, xác suất thấp.

#### Chiến lược giảm thiểu

- **Mạng**: Lưu dữ liệu tạm thời trên Raspberry Pi khi mất kết nối.
- **Cảm biến**: Kiểm tra định kỳ và chuẩn bị linh kiện thay thế.
- **Chi phí**: Thiết lập AWS Budgets và tối ưu tài nguyên theo mức sử dụng thực tế.

#### Kế hoạch dự phòng

- Quay lại thu thập thủ công nếu hệ thống AWS gặp sự cố nghiêm trọng.
- Sử dụng CloudFormation/CDK để khôi phục cấu hình hạ tầng khi cần.

### 8. Kết quả kỳ vọng

#### Cải tiến kỹ thuật

Hệ thống thay thế quy trình thu thập thủ công bằng giám sát thời gian thực, lưu trữ tập trung và phân tích dữ liệu trên AWS. Kiến trúc có thể mở rộng từ 5 trạm lên 10-15 trạm.

#### Giá trị dài hạn

Nền tảng tạo ra bộ dữ liệu thời tiết trong 1 năm cho nghiên cứu AI, đồng thời có thể tái sử dụng làm nền tảng cho các dự án IoT hoặc phân tích dữ liệu trong tương lai.
