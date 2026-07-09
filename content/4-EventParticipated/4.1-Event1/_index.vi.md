---
title: "FCAJ Community Day"
date: 2026-05-23
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---

# Bài thu hoạch: FCAJ Community Day

### Thông tin sự kiện

**FCAJ Community Day** là sự kiện thuộc chương trình AWS First Cloud AI Journey, được tổ chức vào sáng Thứ Bảy, ngày 23/05/2026 tại tầng 26, Bitexco Financial Tower, Thành phố Hồ Chí Minh.

Sự kiện diễn ra từ 09:00 đến 12:00, với nhiều phiên chia sẻ xoay quanh AI, cloud, CloudFront, Amazon QuickSight, LLM, multi-agent system và kinh nghiệm xây dựng sản phẩm trong thời gian ngắn.

### Mục tiêu khi tham gia

Khi tham gia sự kiện này, tôi mong muốn mở rộng góc nhìn về cách AI và cloud được ứng dụng trong thực tế, không chỉ ở mức lý thuyết mà còn qua kinh nghiệm của các diễn giả đang làm việc trong ngành. Ngoài ra, đây cũng là cơ hội để tôi kết nối với cộng đồng FCAJ, học hỏi định hướng nghề nghiệp và hiểu rõ hơn cách xây dựng sản phẩm công nghệ có giá trị.

### Nội dung chính của sự kiện

#### Context Is Everything: Making AI Actually Work for You

Phiên chia sẻ của anh Tinh Truong giúp tôi hiểu rằng AI không chỉ phụ thuộc vào mô hình mạnh hay prompt hay, mà còn phụ thuộc rất lớn vào ngữ cảnh. Nếu thiếu context, AI dễ đưa ra câu trả lời chung chung, sai hướng hoặc không phù hợp với nhu cầu thực tế.

Điểm tôi ấn tượng nhất là ý tưởng chuyển từ prompt đơn lẻ sang hệ thống có memory và context dài hạn. Điều này giúp tôi nhìn AI như một công cụ cần được thiết kế luồng sử dụng cẩn thận, thay vì chỉ hỏi đáp ngẫu nhiên.

#### Friendly AI Assistant with Amazon Quick

Phiên chia sẻ của anh Anh Pham giới thiệu cách các công cụ AI có thể hỗ trợ phân tích dữ liệu, tạo workflow và xây dựng báo cáo bằng ngôn ngữ tự nhiên. Nội dung về Quick Chat Agent, Quick Flows, Quick Spaces và QuickSight giúp tôi thấy rõ hơn xu hướng đưa AI vào công việc phân tích dữ liệu hằng ngày.

Từ phần này, tôi rút ra rằng AI không chỉ dùng để sinh nội dung, mà còn có thể giúp người dùng khám phá dữ liệu, tự động hóa thao tác và biến insight cá nhân thành tri thức dùng chung cho cả nhóm.

#### From Edge To Origin: CloudFront as Your Foundation

Phiên chia sẻ của anh Thinh Nguyen tập trung vào Amazon CloudFront và vai trò của edge network trong việc tăng hiệu năng, giảm chi phí, tăng độ tin cậy và cải thiện bảo mật cho hệ thống.

Trước sự kiện, tôi thường nghĩ CloudFront chủ yếu dùng để cache nội dung tĩnh. Sau phiên này, tôi hiểu rằng CloudFront có thể trở thành lớp nền quan trọng cho nhiều loại workload, đặc biệt khi cần tối ưu trải nghiệm người dùng, bảo vệ origin và kiểm soát lưu lượng truy cập.

#### 36 hrs with LotusHacks: Building UTMorpho from Idea to Reality

Phần chia sẻ của Team VIB mang lại góc nhìn rất thực tế về quá trình xây dựng sản phẩm trong hackathon. Nhóm trình bày từ lúc hình thành ý tưởng, xác định vấn đề, phát triển trong 36 giờ, gặp lỗi, điều chỉnh hướng đi và hoàn thiện demo.

Điều tôi học được là khi thời gian bị giới hạn, nhóm cần ưu tiên vấn đề cốt lõi, chia nhỏ công việc rõ ràng và liên tục kiểm tra sản phẩm thay vì cố làm quá nhiều tính năng. Đây là bài học hữu ích cho giai đoạn làm project nhóm của tôi.

#### Non-Determinism of "Deterministic" LLM Settings

Phiên chia sẻ của anh Duc Dao giúp tôi hiểu sâu hơn về cách LLM chọn token tiếp theo và vì sao thiết lập như temperature = 0 không phải lúc nào cũng đảm bảo kết quả hoàn toàn giống nhau.

Nội dung này khá quan trọng vì khi xây dựng hệ thống dùng AI, mình không nên xem AI như một hàm luôn trả về kết quả cố định. Thay vào đó, cần có cách kiểm thử, logging, guardrail và chiến lược giảm rủi ro để hệ thống ổn định hơn.

#### Enterprise-Grade Multi-Agent System: The Case of Startup Credit Scoring

Phiên chia sẻ của chị Vy Lam giúp tôi hình dung cách multi-agent system có thể được áp dụng trong bài toán doanh nghiệp, cụ thể là đánh giá tín dụng startup. Nội dung về single agent, multi-agent paradigm, virtual credit committee, guardrails và compliance cho thấy việc dùng AI trong doanh nghiệp cần nhiều lớp kiểm soát hơn so với demo đơn giản.

Tôi đặc biệt chú ý đến phần guardrails và compliance, vì đây là yếu tố quan trọng khi đưa AI vào các quy trình có rủi ro cao như tài chính, dữ liệu nhạy cảm hoặc ra quyết định kinh doanh.

### Những kiến thức và bài học rút ra

* AI muốn hiệu quả cần có context tốt, dữ liệu phù hợp và luồng sử dụng được thiết kế rõ ràng.
* CloudFront không chỉ là CDN cho file tĩnh mà còn là lớp bảo vệ, tối ưu chi phí, hiệu năng và độ tin cậy cho ứng dụng.
* Các công cụ AI trong phân tích dữ liệu giúp rút ngắn khoảng cách giữa người dùng nghiệp vụ và hệ thống kỹ thuật.
* Khi xây dựng sản phẩm trong thời gian ngắn, cần tập trung vào vấn đề chính, demo được luồng cốt lõi và chấp nhận lặp lại nhanh.
* LLM có tính không xác định nhất định, vì vậy hệ thống dùng AI cần logging, kiểm thử và cơ chế kiểm soát đầu ra.
* Multi-agent system trong môi trường doanh nghiệp cần quan tâm đến vai trò từng agent, dữ liệu đầu vào, guardrails, compliance và khả năng vận hành lâu dài.

### Liên hệ với dự án nhóm

Sự kiện này có liên quan trực tiếp đến dự án nhóm của tôi, đặc biệt là phần Post-processing & AI mà tôi phụ trách. Sau khi nghe các phiên về context, LLM và multi-agent system, tôi nhận ra rằng việc gọi AI API để tóm tắt lỗi không nên chỉ gửi log thô một cách đơn giản.

Thay vào đó, Lambda post-processing cần chuẩn bị context tốt hơn: thông tin test case, lỗi chính, thời gian chạy, log quan trọng, trạng thái hệ thống và dữ liệu liên quan trong DynamoDB. Report lưu trên S3 cũng nên có cấu trúc rõ ràng để người dùng dễ hiểu nguyên nhân lỗi và hướng xử lý.

Ngoài ra, phần chia sẻ về CloudFront và bảo mật giúp tôi nhìn lại kiến trúc nhóm theo hướng an toàn hơn: không public tài nguyên không cần thiết, dùng Presigned URL cho report, Secrets Manager cho API key và VPC Endpoint cho các dịch vụ nội bộ.

### Cảm nhận cá nhân

FCAJ Community Day là một sự kiện rất hữu ích với tôi vì nội dung không bị giới hạn trong một dịch vụ AWS cụ thể, mà mở rộng sang cách tư duy khi làm sản phẩm AI và cloud. Tôi thích cách các diễn giả kết hợp giữa kiến thức kỹ thuật, kinh nghiệm thực tế và định hướng nghề nghiệp cho sinh viên.

Sau sự kiện, tôi có thêm động lực để hoàn thiện project nhóm theo hướng thực tế hơn: không chỉ chạy được demo, mà còn cần chú ý đến bảo mật, chi phí, trải nghiệm người dùng, chất lượng report và cách AI được tích hợp vào hệ thống.
