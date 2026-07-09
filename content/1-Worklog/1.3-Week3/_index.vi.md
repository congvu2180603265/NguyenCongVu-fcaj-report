---
title: "Worklog Tuần 3"
date: 2026-05-01
weight: 1
chapter: false
pre: " <b> 1.3. </b> "
---
### Mục tiêu tuần:
Tập trung vào các phần nâng cao hơn của Module 02, đặc biệt là DNS Resolution và VPC Peering. Vì nội dung mạng khá khó nên mình chọn làm kỹ một vài lab cốt lõi thay vì học dàn trải.

### Công việc thực hiện trong tuần:
| Thứ | Ngày | Công việc | Nguồn tài liệu |
|---|---|---|---|
| Thứ Sáu | 01/05/2026 | Xem hướng dẫn Lab 10 (DNS Resolution) và Lab 19 (VPC Peering), đồng thời ghi chú các bước chuẩn bị và điều kiện cần có trước khi thực hành | YouTube (playlist FCJ) |
| Thứ Bảy | 02/05/2026 | Ôn lại hai lab một lần nữa và tìm hiểu trước về Route 53 Resolver, Inbound Endpoint và Private Hosted Zone để khi làm lab không bị bỡ ngỡ |  |
| Chủ Nhật | 03/05/2026 | Bắt đầu Lab 10; vì template CloudFormation mẫu bị lỗi nên mình tìm cách tạo Inbound Endpoint thủ công thay vì phụ thuộc hoàn toàn vào script có sẵn | awsstudygroup.com (Lab 10) |
| Thứ Hai | 04/05/2026 | Tiếp tục Lab 10 bằng cách cấu hình Private Hosted Zone và liên kết nó với VPC để hoàn tất phần phân giải DNS nội bộ | awsstudygroup.com (Lab 10) |
| Thứ Ba | 05/05/2026 | Hoàn thành Lab 10 và đọc thêm tài liệu để nắm rõ toàn bộ luồng phân giải tên miền nội bộ trong VPC hoạt động như thế nào | awsstudygroup.com (Lab 10) |
| Thứ Tư | 06/05/2026 | Thực hành Lab 19 về VPC Peering; ban đầu hai EC2 ở hai VPC khác nhau không ping được nhau, nên mình dùng traceroute để lần ra nguyên nhân, phát hiện lỗi ở Route Table rồi sửa lại thành công | awsstudygroup.com (Lab 19) |
| Thứ Năm | 07/05/2026 | Ôn lại toàn bộ phần mạng đã học và cân nhắc tiến độ, sau đó tạm gác Lab 20 (Transit Gateway) để dành thời gian cho các module tiếp theo |  |

### Kết quả đạt được tuần 3:
* Hoàn thành Lab 10 về DNS Resolution và Lab 19 về VPC Peering.
* Biết cách xử lý khi CloudFormation template bị lỗi bằng cách tự cấu hình Inbound Endpoint và Private Hosted Zone.
* Hiểu rõ hơn cơ chế phân giải DNS nội bộ trong VPC.
* Sử dụng traceroute để chẩn đoán và khắc phục lỗi định tuyến trong mô hình VPC Peering.
* Cải thiện khả năng tự tra cứu và xử lý lỗi khi lab không chạy đúng như hướng dẫn mẫu.


