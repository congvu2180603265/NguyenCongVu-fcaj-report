---
title: "Worklog Tuần 5"
date: 2026-05-15
weight: 1
chapter: false
pre: " <b> 1.5. </b> "
---
### Mục tiêu tuần:
Tập trung vào ba mảng chính của Module 05 là IAM, mã hóa dữ liệu và giám sát hệ thống, đồng thời chỉ giữ lại các lab cốt lõi để học cho chắc.

### Công việc thực hiện trong tuần:
| Thứ | Ngày | Công việc | Nguồn tài liệu |
|---|---|---|---|
| Thứ Sáu | 15/05/2026 | Xem lại phần tổng quan của Module 05, hệ thống hóa các ý chính về phân quyền, danh tính và khóa mã hóa | youtube (playlist FCJ) |
| Thứ Bảy | 16/05/2026 | Làm Lab 48: thử cách EC2 truy cập S3 bằng Access Key/Secret Key và so sánh với phương án gắn IAM Role trực tiếp vào EC2 | awsstudygroup.com (Lab 48) |
| Chủ Nhật | 17/05/2026 | Làm lại Lab 48 để củng cố kiến thức, từ đó thấy rõ lý do không nên nhúng Access Key thẳng vào ứng dụng | awsstudygroup.com (Lab 48) |
| Thứ Hai | 18/05/2026 | Làm Lab 33: tạo KMS Key, bật mã hóa cho object trên S3 và kiểm tra quyền truy cập dữ liệu đã được mã hóa | awsstudygroup.com (Lab 33) |
| Thứ Ba | 19/05/2026 | Kiểm thử lại phần KMS để xác nhận rằng S3 access thôi là chưa đủ nếu người dùng không có quyền với KMS Key | awsstudygroup.com (Lab 33) |
| Thứ Tư | 20/05/2026 | Làm Lab 08: quan sát Metrics và Logs, sau đó cấu hình Alarm và Dashboard cơ bản cho CloudWatch | awsstudygroup.com (Lab 8) |
| Thứ Năm | 21/05/2026 | Xem trước Module 06 về RDS và Aurora, đồng thời gác lại các lab IAM nâng cao để dành thời gian cho phần Database | youtube (playlist FCJ) |

### Kết quả đạt được tuần 5:
* Hiểu rõ hơn cách IAM Role hoạt động và nhận ra rủi ro khi dùng Access Key cố định trong ứng dụng.
* Hoàn thành Lab 48 và biết dùng IAM Role trên EC2 để truy cập S3 an toàn hơn.
* Hoàn thành Lab 33 và nắm được cách AWS KMS bổ sung lớp bảo vệ cho dữ liệu trên S3.
* Hoàn thành Lab 08 và biết thiết lập theo dõi cơ bản bằng CloudWatch với Metrics, Logs, Alarm và Dashboard.
* Chủ động thu gọn phạm vi học để giữ nhịp độ ổn định và chừa thời gian cho Module 06.


