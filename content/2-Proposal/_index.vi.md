---
title: "Bản đề xuất"
date: 2026-06-19
weight: 2
chapter: false
pre: " <b> 2. </b> "
---


# Cloud-Native E2E Testing Platform  
## Giải pháp AWS Serverless cho kiểm thử End-to-End tự động và quản lý báo cáo thông minh.  

### 1. Tóm Tắt Dự Án   
Hệ thống là nền tảng kiểm thử tự động End-to-End (E2E) cho Website, được xây dựng nhằm loại bỏ hoàn toàn việc kỹ sư phải tự tay ngồi canh và chạy kiểm thử mỗi khi có thay đổi triển khai. Playwright chạy bên trong container Docker để giả lập hành vi người dùng thật trên trình duyệt (điều hướng, nhập liệu, kiểm tra kết quả hiển thị) sau đó một bước tóm tắt bằng AI sẽ chuyển log kỹ thuật thô thành nội dung dễ hiểu gửi qua email.

Toàn bộ hệ thống vận hành trên AWS theo kiến trúc hướng sự kiện, không máy chủ thường trực (serverless-first): Amazon EventBridge kích hoạt các lần kiểm thử theo lịch, Amazon SQS tách rời việc tiếp nhận yêu cầu khỏi việc thực thi, một hàm AWS Lambda (Coordinator) khởi tạo một tác vụ Amazon ECS Fargate độc lập cho mỗi lần kiểm thử, và tác vụ này tự động tắt sau khi báo cáo được tạo xong. Nhờ vậy hệ thống chỉ tốn chi phí đúng những phút thực sự chạy test, thay vì phải trả tiền cho một server chạy 24/7 mà phần lớn thời gian không làm gì. Việc truy cập Dashboard quản trị được phân quyền theo 3 vai trò (Admin, QA/Tester, Developer) thông qua Amazon Cognito.
  
### 2. Tuyên bố vấn đề  
**Vấn đề hiện tại**  
Kiểm thử End-to-End thủ công đòi hỏi tester phải tự tay chạy kịch bản test mỗi khi có thay đổi được triển khai cách làm này không thể mở rộng khi số lượng ứng dụng và bộ test case ngày càng tăng. Không có hệ thống tập trung để lên lịch kiểm thử, theo dõi xu hướng Pass/Fail theo lịch sử hay tự động thông báo đến đúng người liên quan.Việc duy trì một server chạy liên tục chỉ để sẵn sàng cho lần kiểm thử tiếp theo gây lãng phí chi phí vận hành vì phần lớn thời gian server ở trạng thái nhàn rỗi.  

**Giải pháp**  
Hệ thống tiếp nhận hai nguồn kích hoạt lịch trình tự động dạng cron (Amazon EventBridge) kích hoạt thủ công theo yêu cầu (Amazon API Gateway) và chuẩn hóa cả hai vào chung một hàng đợi Amazon SQS kèm theo Dead Letter Queue (DLQ) để lưu lại các yêu cầu xử lý thất bại. Một hàm AWS Lambda (Coordinator) đóng vai trò consumer của hàng đợi, gọi API RunTask để khởi tạo một tác vụ Amazon ECS Fargate ngắn hạn, chạy bộ kiểm thử Playwright đã được đóng gói trong Docker image lưu tại Amazon ECR. Sau khi hoàn tất, tác vụ ghi báo cáo HTML/JSON vào một bucket Amazon S3 riêng tư và gửi log thời gian thực đến Amazon CloudWatch, rồi tự động tắt.

Một hàm AWS Lambda hậu kỳ (Post-processing) đọc log thô, lọc bớt dữ liệu không cần thiết rồi gửi đến một AI API bên ngoài để sinh ra đoạn tóm tắt ngắn gọn bằng ngôn ngữ tự nhiên kèm theo cơ chế dự phòng (fallback) vẫn gửi báo cáo gốc qua email nếu lời gọi AI thất bại hoặc quá thời gian chờ. Amazon SES đảm nhiệm gửi email thông báo cuối cùng gồm số liệu Pass/Fail, đường liên kết S3 Presigned URL có giới hạn thời gian và phần tóm tắt do AI tạo ra đến đúng danh sách người nhận đã đăng ký cho ứng dụng đó.
  
**Lợi ích và hoàn vốn đầu tư**  
* Loại bỏ việc chạy kiểm thử thủ công: không còn phải tự kích hoạt hay ngồi canh kết quả. 
* Rút ngắn thời gian phản hồi: chuyển một quy trình thủ công có thể mất vài giờ thành một lần chạy tự động chỉ vài phút.
* Tối ưu chi phí: chi phí tính theo mức sử dụng thực tế thay vì duy trì server 24/7.
* Lưu trữ lịch sử đầy đủ: mọi lần chạy đều được lưu vĩnh viễn trong Amazon DynamoDB, phục vụ phân tích xu hướng chất lượng điều gần như không khả thi khi còn ghi chép rời rạc bằng Excel.
* Giải phóng thời gian của đội QA để tập trung thiết kế kịch bản kiểm thử tốt hơn thay vì lặp lại thao tác kiểm tra thủ công.

### 3. Kiến trúc giải pháp  
Hệ thống được chia thành Backend Engine (lên lịch, thực thi và tạo báo cáo kiểm thử) và Dashboard Console (giao diện dành cho 3 vai trò Admin, QA/Tester, Developer). Mọi yêu cầu đều đi qua đúng một luồng xử lý duy nhất không có đường tắt nào bỏ qua chuỗi SQS -> Lambda Coordinator -> Fargate, dù kích hoạt theo lịch hay thủ công.  

![Sơ đồ kiến trúc hệ thống](/images/2-Proposal/architecture.png)

**Các dịch vụ AWS sử dụng**

| Dịch vụ AWS | Vai trò trong kiến trúc |
|---|---|
| Amazon EventBridge | Lập lịch và phát sinh sự kiện kiểm thử định kỳ (biểu thức cron). |
| Amazon API Gateway (HTTP API) | Tiếp nhận yêu cầu kích hoạt thủ công và các lời gọi API từ Dashboard, xác thực qua Lambda Authorizer. |
| Amazon SQS + DLQ | Đệm và chuẩn hóa mọi yêu cầu kiểm thử, DLQ lưu lại các lần chạy thất bại lặp lại. |
| AWS Lambda (Coordinator) | Tiêu thụ hàng đợi và gọi ECS RunTask để khởi tạo tác vụ Fargate. |
| Amazon ECS Fargate | Chạy container Docker/Playwright trong Private Subnet, tự tắt sau khi hoàn tất. |
| Amazon ECR | Lưu trữ Docker image chứa bộ chạy kiểm thử Playwright. |
| Amazon S3 (2 bucket) | Một bucket lưu frontend Dashboard tĩnh; một bucket riêng tư lưu báo cáo và tệp kiểm thử. |
| Amazon CloudWatch | Thu thập log, metric, cảnh báo khi tác vụ chạy quá thời gian hoặc DLQ tồn đọng. |
| AWS Lambda (Post-processing) | Làm sạch log, gọi AI API bên ngoài, chuẩn bị nội dung thông báo. |
| Google Gemini (bên ngoài AWS) | Sinh nội dung tóm tắt log bằng ngôn ngữ tự nhiên (thay thế Amazon Bedrock do giới hạn free tier). |
| NAT Gateway (Public Subnet) | Cho phép Lambda Post-processing trong Private Subnet gọi ra AI API bên ngoài. |
| Amazon SES | Gửi email kết quả trực tiếp đến người nhận đã đăng ký. |
| Amazon DynamoDB | Lưu lịch sử kiểm thử, Audit Log và dữ liệu cấu hình hệ thống. |
| Amazon CloudFront | Phân phối frontend Dashboard tĩnh; kết hợp với WAF phạm vi CLOUDFRONT. |
| Amazon Cognito | Xác thực người dùng Dashboard và cấp token phục vụ phân quyền theo vai trò. |
| AWS Secrets Manager | Lưu trữ khóa AI API và các cấu hình nhạy cảm khác. |
| Amazon VPC + VPC Endpoints (PrivateLink) | Cô lập Fargate trong Private Subnet, các lời gọi nội bộ AWS không đi qua Internet công cộng. |
| AWS WAF (phạm vi CloudFront + Regional) | Hai Web ACL riêng biệt bảo vệ CloudFront và endpoint API Gateway. |

**Thiết kế thành phần**
* Tầng kích hoạt: EventBridge và API Gateway đều đẩy sự kiện vào cùng một hàng đợi SQS.
* Tầng thực thi: Lambda Coordinator khởi tạo một tác vụ ECS Fargate cho mỗi lần chạy, tác vụ kéo image Playwright từ ECR và chạy headless trong Private Subnet.
* Tầng báo cáo: Báo cáo HTML/JSON được lưu vào S3 riêng tư, log được truyền vào CloudWatch theo thời gian thực.
* Tầng AI: Một Lambda thứ hai làm sạch log và gọi AI API qua NAT Gateway, kèm cơ chế circuit-breaker để fallback về báo cáo gốc nếu AI lỗi.
* Tầng thông báo: SES gửi email đến người nhận đã đăng ký cho ứng dụng đó, kèm S3 Presigned URL có giới hạn thời gian.
* Tầng truy cập: Cognito thực thi nhất quán 3 vai trò (Admin, QA/Tester, Developer) tại ranh giới API Gateway.

### 4. Triển khai kỹ thuật  
**Các giai đoạn triển khai**  
1. *Nghiên cứu và vẽ kiến trúc*: .  
2. *Tính toán chi phí và kiểm tra tính khả thi*:   
3. *Điều chỉnh kiến trúc để tối ưu chi phí/giải pháp*: 
4. *Phát triển, kiểm thử, triển khai*: 
* Thiết lập môi trường & Container: Cấu hình IAM/AWS CLI, xây dựng Docker/Playwright runner (Dockerfile, entrypoint.js, playwright.config.js).
* Luồng sự kiện & Coordinator: Thiết lập SQS + DLQ và Lambda Coordinator gọi ECS RunTask.
* Lưu trữ & Giám sát: S3 (frontend + báo cáo), log group và alarm CloudWatch, bảng DynamoDB lưu lịch sử và audit log.
* Tóm tắt bằng AI: Lambda hậu kỳ tích hợp Secrets Manager cho khóa Google Gemini API và cơ chế fallback dạng circuit-breaker.
* Dashboard & Phân quyền: Lưu trữ tĩnh CloudFront + S3, Cognito user pool.
* Tăng cường bảo mật: Chính sách IAM theo nguyên tắc đặc quyền tối thiểu, VPC Endpoints.
* Kiểm thử tích hợp & Demo: Chạy thử end-to-end kiểm chứng toàn bộ pipeline.

**Yêu cầu kỹ thuật**  
* *Bộ chạy kiểm thử*: Node.js, Playwright, Docker (multi-stage build) để tạo image đẩy lên ECR.
* *Logic Coordinator*: Dùng AWS SDK gọi ECS RunTask từ hàm Lambda được kích hoạt bởi SQS.
* *Hạ tầng dạng mã (IaC)*: Khuyến nghị dựng tài nguyên bằng IaC (ví dụ AWS CDK/CloudFormation) để đảm bảo khả năng tái lập giữa các môi trường.
* *Nền tảng bảo mật*: Vai trò IAM giới hạn phạm vi cho từng thành phần (không dùng chính sách *FullAccess), Secrets Manager cho thông tin xác thực và VPC Endpoints cho các lời gọi dịch vụ nội bộ.
 
### 5. Lộ trình & Mốc triển khai  
**Giai Đoạn 1: Tự Học Kiến Thức Nền Tảng AWS (Tuần 1 – 8)**
Giai đoạn tự học của mỗi thành viên trong nhóm, lộ trình học của mỗi người khác nhau nhưng nhìn chung tổng thể cả nhóm đều đạt được được nền tảng cần thiết để chuẩn bị cho đề tài workshop:
 * Nền tảng AWS & bảo mật: IAM (user, role, policy), VPC & networking cơ bản, Secrets Manager.
 * Compute & container hóa: Docker, Amazon ECS/Fargate, Amazon ECR.
 * Serverless & tích hợp sự kiện: AWS Lambda, Amazon API Gateway, Amazon EventBridge, Amazon SQS.
 * Lưu trữ & dữ liệu: Amazon S3, Amazon DynamoDB, Amazon CloudFront.
 * Giám sát & xác thực người dùng: Amazon CloudWatch, Amazon Cognito.

**Giai đoạn 2: Triền khai**
| Tuần | Nội dung |
| :--- | :--- |
| 9 | Họp bàn và cùng đề xuất đề tài kiểm thử tự động bằng playwright, cùng nhau đánh giá tính khả thi và đưa ra hướng phát triển phù hợp.Sau khi chốt được đề tài thì lên kế hoạch phân chia công việc và phát thảo sơ cơ bản sơ đồ kiến trúc. |
| 10 | Cùng tự nghiên cứu, tìm hiểu và tham khảo nhiều sơ đồ kiến trúc để có thể chỉnh sửa những điểm sai trong quá trình vẽ sơ đồ kiến trúc.Sau khi nộp bản vẽ sơ đồ kiến trúc đầu tiên vào group để xin các anh chị đánh giá, phát hiện còn nhiều lỗi sai nên nhóm cùng tiếp tiếp rà soát và quyết định chuyển qua cách vẽ chia thành các layer để sơ đồ được rõ ràng hơn. |
| 11 | Cùng tiếp tục hoàn thiện sơ đồ kiến trúc, đã chỉnh sửa nhiều các lỗi sai, sau lần nộp xin đánh giá lần thứ 2, đã được các anh chị đánh giá ổn, sơ đồ kiến trúc đã hoàn thiện, nhóm bắt tay vào lên kế hoạch và phân chia các bước thực thiện cho từng thành viên.Mỗi thành viên tự chuẩn bị các file, các cấu hình cần thiết để thực hiện giai đoạn triển khai. |
| 12 | Sau khi hoàn tất giai đoạn chuẩn bị, các thành viên bước vào giai đoạn triển khai phần thực hiện được phân công.Sau khi chạy thử dự án nhóm cùng nhau bắt đầu tìm và sửa các bug còn tồn tại, kiểm thử từng chức năng xem còn lỗi logic nào không, cuối cùng hoàn thiện được workshop và bắt đầu viết báo cáo. |

### 6. Ước tính ngân sách  
Bảng dưới đây được lập từ kết quả thực tế trên AWS Pricing Calculator (Asia Pacific - Singapore), dựa trên giả định: ~50 tác vụ Fargate/ngày (thời lượng trung bình 1 phút/tác vụ), ~10.000 lượt gọi Lambda/tháng, 5 người dùng Dashboard (Cognito MAU), và ~4.500 email gửi qua SES/tháng

| Dịch vụ AWS | Cấu hình chính trên AWS Pricing Calculator | Chi phí/tháng (USD) |
|-------------|--------------------------------------------|--------------------:|
| AWS Fargate | Linux/x86, 50 task/ngày, 1 phút/task, 2 GB RAM, 20 GB Ephemeral Storage | 1.88 |
| AWS Lambda | 10.000 request/tháng, 512 MB Ephemeral Storage | 0.00 |
| Amazon SQS | 0.0045 triệu Standard Request/tháng | 0.00 |
| Amazon S3 – Frontend | 1 GB Storage, 50 PUT, 1.500 GET/tháng | 0.03 |
| Amazon S3 – Reports | 3 GB Storage, 37.500 PUT, 200 GET/tháng | 0.26 |
| Amazon CloudWatch | 2.2 GB Log Ingested, 1 Dashboard, 3 Standard Alarm, 100 API request khác | 1.85 |
| Amazon DynamoDB (On-Demand) | 1 GB Storage, kích thước item trung bình 5 KB | 1.88 |
| Amazon VPC – PrivateLink | 3 VPC Interface Endpoint/Region | 0.05 |
| AWS Secrets Manager | 1 Secret, lưu 30 ngày, 1.500 API Call/tháng | 0.41 |
| Amazon Cognito | 5 MAU, bật Advanced Security Features | 0.26 |
| Amazon CloudFront | 2.000 HTTPS Request, 1 GB dữ liệu từ Origin, 1 GB dữ liệu ra Internet | 0.11 |
| Amazon API Gateway (HTTP API) | 0.0075 triệu request/tháng, 34 KB/request | 0.01 |
| Amazon SES | 4.500 email gửi từ Lambda/tháng | 0.45 |
| **Cộng dồn (Calculator)** | — | **7.19** |
| NAT Gateway *(lập lịch tạo/xóa để tối ưu chi phí)* | Chỉ chạy trong khung giờ cần gọi AI API, không chạy 24/7 | **15.045** |
| Google Gemini AI API | free tier | 0.0.0  |
| **Tổng cộng** | — | **22.24** |

Với mức 22.24 USD/tháng, tổng chi phí AWS trong 12 tháng ước tính khoảng 266.82 USD.

### 7. Đánh giá rủi ro  
**Ma trận rủi ro**  
| Rủi ro | Mức độ ảnh hưởng | Xác suất xảy ra |
|---------|------------------|-----------------|
| Tác vụ Fargate chạy vượt quá thời gian dự kiến hoặc bị timeout | Trung bình | Trung bình |
| AI API bên ngoài (OpenAI) không khả dụng hoặc bị giới hạn tốc độ | Thấp *(đã có fallback)* | Trung bình |
| Vai trò IAM được cấp quá nhiều quyền trong quá trình phát triển | **Cao** | Trung bình |
| Tin nhắn tồn đọng trong DLQ mà không được cảnh báo | Trung bình | Thấp |
| Chi phí AWS vượt dự kiến do cấu hình sai NAT Gateway hoặc thời gian lưu CloudWatch | Trung bình | Thấp |
| Độ trễ khi QA kích hoạt kiểm thử đột xuất ngoài khung giờ NAT Gateway đã lên lịch | Trung bình | Trung bình |
| Website demo hoạt động không ổn định (nếu sử dụng website của bên thứ ba) | Trung bình | Trung bình |  

**Chiến lược giảm thiểu**  
* Thiết lập CloudWatch Alarm cho thời lượng tác vụ ECS và độ sâu DLQ để phát hiện sớm các lần chạy bị treo hoặc thất bại lặp lại.
* Giữ bước tóm tắt AI phía sau cơ chế circuit-breaker để báo cáo gốc vẫn được gửi email nếu lời gọi AI thất bại.
* Áp dụng nguyên tắc đặc quyền tối thiểu (least-privilege IAM) ngay từ đầu triển khai, không để đến giai đoạn dọn dẹp sau này.
* Thiết lập sớm luồng cảnh báo DLQ -> CloudWatch Alarm -> SNS để không lần chạy thất bại nào bị bỏ sót âm thầm.
* Sử dụng website demo tự dựng thay vì domain thật của bên thứ ba, tránh vấn đề chống bot và thay đổi giao diện khó lường.
* Nếu test được kích hoạt thủ công ngoài khung giờ đã lên lịch cho NAT Gateway, hệ thống có thể kiểm tra trạng thái NAT trước khi gọi AI API, hoặc chấp nhận độ trễ 1-3 phút để NAT khởi tạo, cần thống nhất phương án với nhóm trước khi triển khai.  

**Kế hoạch dự phòng**  
* Nếu nhà cung cấp AI không phản hồi, hệ thống vẫn gửi báo cáo Pass/Fail gốc qua email.
* Nếu một tác vụ Fargate bị treo, CloudWatch alarm sẽ kích hoạt và tác vụ có thể bị buộc dừng mà không ảnh hưởng đến các lần chạy khác (mỗi lần chạy được cô lập độc lập).
* Nếu chi phí AWS có xu hướng tăng bất thường, cảnh báo ngân sách (budget alert) sẽ phát hiện đủ sớm để điều chỉnh thời gian lưu log hoặc mức sử dụng NAT.  

### 8. Kết quả kỳ vọng  
**Cải tiến kỹ thuật**: 
* Kiểm thử E2E thủ công được thay thế bằng pipeline tự động hướng sự kiện, không còn hạ tầng nhàn rỗi thường trực.
* Mọi lần kiểm thử — dù Đạt hay Không đạt — đều được lưu trữ vĩnh viễn, phục vụ phân tích xu hướng theo thời gian.
* Phân quyền theo vai trò (Admin / QA-Tester / Developer) được thực thi nhất quán tại ranh giới API.  

**Giá trị dài hạn**:
* Trở thành kiến trúc tham khảo có thể tái sử dụng cho các dự án serverless, hướng sự kiện khác của nhóm trong tương lai.
* Dữ liệu lịch sử kiểm thử tích lũy (DynamoDB) là nền tảng cho việc phân tích các lỗi lặp lại sau này.
* Minh chứng cho một mô hình chi phí rõ ràng, có căn cứ (trả theo mức sử dụng) so với server kiểm thử chạy liên tục truyền thống.