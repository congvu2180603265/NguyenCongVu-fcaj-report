---
title: "Worklog Tuần 8"
date: 2026-06-05
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Mục tiêu tuần:
Tuần 8 đánh dấu bước chuyển từ học lab sang suy nghĩ theo hướng sản phẩm. Tôi bắt đầu tham gia cùng nhóm để tìm ý tưởng dự án, đồng thời tự đào sâu các phần có khả năng liên quan đến vai trò của mình như kiểm thử tự động, Docker và lưu trữ dữ liệu test bằng DynamoDB.

### Công việc thực hiện trong tuần:
| Thứ | Ngày | Công việc | Nguồn tài liệu |
|---|---|---|---|
| Thứ Sáu | 05/06/2026 | Xem lại DynamoDB và Boto3 dưới góc nhìn ứng dụng: nếu hệ thống sinh ra log hoặc lịch sử chạy test thì nên lưu dữ liệu theo khóa nào, truy vấn ra sao | docs.aws.amazon.com/dynamodb |
| Thứ Bảy | 06/06/2026 | Đọc một số bài AWS blog để tham khảo cách các kiến trúc serverless được triển khai trong bài toán thật, từ đó lấy thêm chất liệu cho ý tưởng nhóm | aws.amazon.com/blogs |
| Chủ Nhật | 07/06/2026 | Tự rà lại networking và security, đặc biệt là các điểm dễ sai khi nhiều dịch vụ giao tiếp với nhau như quyền truy cập, endpoint và cấu hình mạng | Ghi chú cá nhân |
| Thứ Hai | 08/06/2026 | Trao đổi với nhóm về các hướng đề tài có thể làm; cùng lọc ý tưởng dựa trên mức độ khả thi, dịch vụ AWS cần dùng và khả năng viết thành bài blog chia sẻ | aws.amazon.com/blogs |
| Thứ Ba | 09/06/2026 | Bắt đầu tìm hiểu Playwright: cách mô phỏng thao tác người dùng, chạy test tự động và kết hợp với Docker để tạo môi trường kiểm thử ổn định | playwright.dev |
| Thứ Tư | 10/06/2026 | Phác thảo luồng lưu kết quả test vào DynamoDB, bao gồm test case, trạng thái chạy, thời gian thực thi và thông tin lỗi nếu có | docs.aws.amazon.com/dynamodb |
| Thứ Năm | 11/06/2026 | Gom lại các ghi chú kỹ thuật, tự xác định phần việc mình có thể phụ trách và chuẩn bị ý kiến để nhóm chốt hướng triển khai | Ghi chú cá nhân |

### Kết quả đạt được tuần 8:
* Chuyển được tư duy từ làm lab riêng lẻ sang suy nghĩ về một hệ thống có nhiều thành phần phối hợp.
* Có định hướng rõ hơn về phần có thể đóng góp: DynamoDB cho dữ liệu test/log và hỗ trợ kiểm thử tự động.
* Nắm được vai trò cơ bản của Playwright và Docker trong một quy trình test có thể lặp lại.
* Tham gia vào quá trình chọn ý tưởng dự án và chuẩn bị hướng nội dung cho bài blog nhóm.
* Xác định được các mảng cần cẩn thận khi triển khai thực tế, nhất là networking và security.
