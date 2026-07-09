---
title: "Đề xuất dự án"
date: 2026-06-16
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Xây Dựng Nền Tảng Kiểm Thử Tự Động End-to-End Theo Kiến Trúc Cloud-Native Serverless Trên AWS

## 1. Tóm Tắt Dự Án
Hệ thống là nền tảng kiểm thử tự động End-to-End (E2E) cho Website, được xây dựng nhằm loại bỏ hoàn toàn việc kỹ sư phải tự tay ngồi canh và chạy kiểm thử mỗi khi có thay đổi triển khai. Playwright chạy bên trong container Docker để giả lập hành vi người dùng thật trên trình duyệt (điều hướng, nhập liệu, kiểm tra kết quả hiển thị) sau đó một bước tóm tắt bằng AI sẽ chuyển log kỹ thuật thô thành nội dung dễ hiểu gửi qua email.

Toàn bộ hệ thống vận hành trên AWS theo kiến trúc hướng sự kiện, không máy chủ thường trực (serverless-first): Amazon EventBridge kích hoạt các lần kiểm thử theo lịch, Amazon SQS tách rời việc tiếp nhận yêu cầu khỏi việc thực thi, một hàm AWS Lambda (Coordinator) khởi tạo một tác vụ Amazon ECS Fargate độc lập cho mỗi lần kiểm thử, và tác vụ này tự động tắt sau khi báo cáo được tạo xong. Nhờ vậy hệ thống chỉ tốn chi phí đúng những phút thực sự chạy test, thay vì phải trả tiền cho một server chạy 24/7 mà phần lớn thời gian không làm gì. Việc truy cập Dashboard quản trị được phân quyền theo 3 vai trò (Admin, QA/Tester, Developer) thông qua Amazon Cognito.

## 2. Phát Biểu Vấn Đề (Problem Statement)

**Vấn đề hiện tại**
Kiểm thử End-to-End thủ công đòi hỏi tester phải tự tay chạy kịch bản test mỗi khi có thay đổi được triển khai, cách làm này không thể mở rộng khi số lượng ứng dụng và bộ test case ngày càng tăng. Không có hệ thống tập trung để lên lịch kiểm thử, theo dõi xu hướng Pass/Fail theo lịch sử hay tự động thông báo đến đúng người liên quan. Việc duy trì một server chạy liên tục chỉ để sẵn sàng cho lần kiểm thử tiếp theo gây lãng phí chi phí vận hành vì phần lớn thời gian server ở trạng thái nhàn rỗi.

**Giải pháp**
Hệ thống tiếp nhận hai nguồn kích hoạt: lịch trình tự động dạng cron (Amazon EventBridge) và kích hoạt thủ công theo yêu cầu (Amazon API Gateway), rồi chuẩn hóa cả hai vào chung một hàng đợi Amazon SQS kèm theo Dead Letter Queue (DLQ) để lưu lại các yêu cầu xử lý thất bại. Một hàm AWS Lambda (Coordinator) đóng vai trò consumer của hàng đợi, gọi API RunTask để khởi tạo một tác vụ Amazon ECS Fargate ngắn hạn, chạy bộ kiểm thử Playwright đã được đóng gói trong Docker image lưu tại Amazon ECR. Sau khi hoàn tất, tác vụ ghi báo cáo HTML/JSON vào một bucket Amazon S3 riêng tư và gửi log thời gian thực đến Amazon CloudWatch, rồi tự động tắt.

Một hàm AWS Lambda hậu kỳ (Post-processing) đọc log thô, lọc bớt dữ liệu không cần thiết rồi gửi đến Google Gemini (external AI API) để sinh ra đoạn tóm tắt ngắn gọn bằng ngôn ngữ tự nhiên kèm theo cơ chế dự phòng (fallback) vẫn gửi báo cáo gốc qua email nếu lời gọi AI thất bại hoặc quá thời gian chờ. Amazon SES đảm nhiệm gửi email thông báo cuối cùng gồm số liệu Pass/Fail, đường liên kết S3 Presigned URL có giới hạn thời gian và phần tóm tắt do AI tạo ra đến đúng danh sách người nhận đã đăng ký cho ứng dụng đó.

**Lợi ích và hiệu quả đầu tư (ROI)**
* Loại bỏ kiểm thử thủ công: Không còn phải tự kích hoạt hay ngồi canh kết quả kiểm tra.
* Rút ngắn thời gian phản hồi: Chuyển một quy trình thủ công có thể mất vài giờ thành một lần chạy tự động chỉ trong vài phút.
* Tối ưu chi phí hạ tầng: Chi phí tính theo mức sử dụng thực tế (pay-as-you-go) thay vì duy trì server 24/7.
* Lưu trữ lịch sử đầy đủ: Mọi lần chạy đều được lưu vĩnh viễn trong Amazon DynamoDB, phục vụ phân tích xu hướng chất lượng, điều gần như không khả thi khi còn ghi chép rời rạc bằng Excel.
* Giải phóng nguồn lực: Giải phóng thời gian của đội QA để tập trung thiết kế kịch bản kiểm thử tốt hơn thay vì lặp lại thao tác kiểm tra thủ công.

## 3. Kiến Trúc Giải Pháp
Hệ thống được chia thành Backend Engine (lên lịch, thực thi và tạo báo cáo kiểm thử) và Dashboard Console (giao diện dành cho 3 vai trò Admin, QA/Tester, Developer). Mọi yêu cầu đều đi qua đúng một luồng xử lý duy nhất, không có đường tắt nào bỏ qua chuỗi SQS → Lambda Coordinator → Fargate, dù kích hoạt theo lịch hay thủ công.
![Kiến trúc giải pháp tự động kiểm thử Playwright](/images/anhProposal.png)

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

## 4. Triển Khai Kỹ Thuật

**Các giai đoạn triển khai**
Kiến trúc đã trải qua nhiều vòng review và được chốt phương án; nhóm hiện đang ở giai đoạn triển khai thực tế, chia thành các bước sau:
1. Thiết lập môi trường & Container: Cấu hình IAM/AWS CLI, xây dựng Docker/Playwright runner (Dockerfile, entrypoint.js, playwright.config.js).
2. Luồng sự kiện & Coordinator: Thiết lập SQS + DLQ và Lambda Coordinator gọi ECS RunTask.
3. Lưu trữ & Giám sát: S3 (frontend + báo cáo), log group và alarm CloudWatch, bảng DynamoDB lưu lịch sử và audit log.
4. Tóm tắt bằng AI: Lambda hậu kỳ tích hợp Secrets Manager cho khóa Google Gemini API và cơ chế fallback dạng circuit-breaker.
5. Dashboard & Phân quyền: Lưu trữ tĩnh CloudFront + S3, Cognito user pool, Lambda Authorizer và giao diện 3 vai trò (Admin / QA-Tester / Developer).
6. Tăng cường bảo mật: Chính sách IAM theo nguyên tắc đặc quyền tối thiểu, VPC Endpoints và hai phạm vi WAF (CloudFront và Regional).
7. Kiểm thử tích hợp & Demo: Chạy thử end-to-end trên một website demo tự dựng (tránh vấn đề chính sách chống bot của bên thứ ba), kiểm chứng toàn bộ pipeline, chuẩn bị báo cáo.

**Yêu cầu kỹ thuật**
* Bộ chạy kiểm thử: Node.js, Playwright, Docker (multi-stage build) để tạo image đẩy lên ECR.
* Logic Coordinator: Dùng AWS SDK gọi ECS RunTask từ hàm Lambda được kích hoạt bởi SQS.
* Hạ tầng dạng mã (IaC): Khuyến nghị dựng tài nguyên bằng IaC (ví dụ AWS CDK/CloudFormation) để đảm bảo khả năng tái lập giữa các môi trường.
* Nền tảng bảo mật: Vai trò IAM giới hạn phạm vi cho từng thành phần (không dùng chính sách *FullAccess), Secrets Manager cho thông tin xác thực và VPC Endpoints cho các lời gọi dịch vụ nội bộ.

## 5. Lộ Trình & Mốc Triển Khai

| Tuần | Cột mốc | Phụ trách |
|---|---|---|
| | | |

## 6. Ước Tính Ngân Sách (Budget Estimation)

| Dịch vụ AWS | Cấu hình chính đã nhập trên Calculator | Chi phí/tháng (USD) |
|---|---|---|
| AWS Fargate | Linux/x86, 50 task/ngày, 1 phút/task, 2 GB RAM, 20 GB ephemeral storage | 1.88 |
| AWS Lambda | 10.000 request/tháng, 512 MB ephemeral storage | 0.00 |
| Amazon SQS | 0.0045 triệu standard request/tháng | 0.00 |
| Amazon S3 – Frontend | 1 GB storage, 50 PUT, 1.500 GET/tháng | 0.03 |
| Amazon S3 – Reports | 3 GB storage, 37.500 PUT, 200 GET/tháng | 0.26 |
| Amazon CloudWatch | 2.2 GB log ingested, 1 dashboard, 3 standard alarm, 100 API request khác | 1.85 |
| Amazon DynamoDB | On-Demand, 1 GB storage, item trung bình 5 KB | 1.88 |
| Amazon VPC – PrivateLink | 3 VPC Interface Endpoint/region | 0.05 |
| AWS Secrets Manager | 1 secret, lưu 30 ngày, 1.500 API call/tháng | 0.41 |
| Amazon Cognito | 5 MAU, bật Advanced Security Features | 0.26 |
| Amazon CloudFront | 2.000 request HTTPS, 1 GB data ra origin, 1 GB ra Internet | 0.11 |
| Amazon API Gateway | HTTP API, 0.0075 triệu request/tháng, 34 KB/request | 0.01 |
| Amazon SES | 4.500 email gửi từ Lambda/tháng | 0.45 |
| **Cộng dồn (Calculator)** | Các dịch vụ trên AWS Calculator | **7.19** |
| NAT Gateway | Lên lịch tạo/xóa để tối ưu chi phí, chỉ chạy trong khung giờ cần gọi Google Gemini API | 15.045 |
| **Tổng cộng** | Chưa bao gồm chi phí Google Gemini API | **22.24** |

Với mức 22.24 USD/tháng, tổng chi phí AWS trong 12 tháng ước tính khoảng 266.82 USD (chưa gồm chi phí Google Gemini API, do đây là dịch vụ bên thứ ba nằm ngoài AWS Pricing Calculator, cần cộng thủ công dựa trên lượng token thực tế sử dụng).

{{% notice warning %}}
Lưu ý kỹ thuật quan trọng: NAT Gateway không hỗ trợ Start/Stop như EC2 — để đạt được mức chi phí tối ưu 15.045 USD/tháng (thay vì khoảng 43 USD/tháng nếu chạy 24/7), phương án lên lịch cần dùng EventBridge Schedule kết hợp Lambda để tự động tạo (CreateNatGateway) trước khung giờ cần chạy và xóa (DeleteNatGateway) sau khi xong. Đánh đổi của cách này là NAT Gateway mất khoảng 1-3 phút để chuyển sang trạng thái sẵn sàng sau khi tạo, có thể gây độ trễ nếu QA kích hoạt kiểm thử thủ công đột xuất ngoài khung giờ đã lên lịch — cần nêu rõ đánh đổi này trong phần rủi ro của báo cáo.
{{% /notice %}}

## 7. Đánh Giá Rủi Ro (Risk Assessment)

**Ma trận rủi ro**

| Rủi ro | Mức độ ảnh hưởng | Xác suất xảy ra |
|---|---|---|
| Tác vụ Fargate chạy vượt quá thời gian dự kiến / timeout | Trung bình | Trung bình |
| AI API bên ngoài (Google Gemini) không khả dụng hoặc bị giới hạn tốc độ | Thấp (đã có fallback) | Trung bình |
| Vai trò IAM được cấp quá nhiều quyền trong quá trình phát triển | Cao | Trung bình |
| Tin nhắn tồn đọng trong DLQ mà không được cảnh báo | Trung bình | Thấp |
| Chi phí AWS vượt dự kiến do cấu hình sai NAT/thời gian lưu CloudWatch | Trung bình | Thấp |
| Độ trễ khi QA kích hoạt test đột xuất ngoài khung giờ NAT Gateway đã lên lịch | Trung bình | Trung bình |
| Website demo hoạt động không ổn định (nếu dùng site thật của bên thứ ba) | Trung bình | Trung bình |

**Chiến lược giảm thiểu**
* Thiết lập CloudWatch Alarm cho thời lượng tác vụ ECS và độ sâu DLQ để phát hiện sớm các lần chạy bị treo hoặc thất bại lặp lại.
* Giữ bước tóm tắt AI phía sau cơ chế circuit-breaker để báo cáo gốc vẫn được gửi email nếu lời gọi AI thất bại.
* Áp dụng nguyên tắc đặc quyền tối thiểu (least-privilege IAM) ngay từ đầu triển khai, không để đến giai đoạn dọn dẹp sau này.
* Thiết lập sớm luồng cảnh báo DLQ → CloudWatch Alarm → SNS để không lần chạy thất bại nào bị bỏ sót âm thầm.
* Sử dụng website demo tự dựng thay vì domain thật của bên thứ ba, tránh vấn đề chống bot và thay đổi giao diện khó lường.
* Nếu test được kích hoạt thủ công ngoài khung giờ đã lên lịch cho NAT Gateway, hệ thống có thể kiểm tra trạng thái NAT trước khi gọi AI API, hoặc chấp nhận độ trễ 1-3 phút để NAT khởi tạo.

**Kế hoạch dự phòng**
* Nếu nhà cung cấp AI không phản hồi, hệ thống vẫn gửi báo cáo Pass/Fail gốc qua email.
* Nếu một tác vụ Fargate bị treo, CloudWatch alarm sẽ kích hoạt và tác vụ có thể bị buộc dừng mà không ảnh hưởng đến các lần chạy khác (mỗi lần chạy được cô lập độc lập).
* Nếu chi phí AWS có xu hướng tăng bất thường, cảnh báo ngân sách (budget alert) sẽ phát hiện đủ sớm để điều chỉnh thời gian lưu log hoặc mức sử dụng NAT.

## 8. Kết Quả Dự Kiến

**Cải tiến kỹ thuật**
Kiểm thử E2E thủ công được thay thế bằng pipeline tự động hướng sự kiện, không còn hạ tầng nhàn rỗi thường trực. Phân quyền theo vai trò (Admin / QA-Tester / Developer) được thực thi nhất quán tại ranh giới API.

**Giá trị dài hạn**
Trở thành kiến trúc tham khảo có thể tái sử dụng cho các dự án serverless, hướng sự kiện khác của nhóm trong tương lai. Dữ liệu lịch sử kiểm thử tích lũy (DynamoDB) là nền tảng cho việc phân tích các lỗi lặp lại sau này. Minh chứng cho một mô hình chi phí rõ ràng, có căn cứ (trả theo mức sử dụng) so với server kiểm thử chạy liên tục truyền thống.