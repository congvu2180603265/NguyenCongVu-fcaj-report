---
title: "Worklog Tuần 2"
date: 2026-04-24
weight: 1
chapter: false
pre: " <b> 1.2. </b> "
---

### Mục tiêu tuần:
Bắt đầu Module 02 về mạng ảo (VPC), đi chậm từng phần để hiểu rõ cách các thành phần mạng hoạt động với nhau.

### Công việc thực hiện trong tuần:
| Thứ | Ngày | Công việc | Nguồn tài liệu |
|---|---|---|---|
| Thứ Sáu | 24/04/2026 | Xem bài giảng Module 02 về Amazon VPC; ghi lại sơ đồ mạng tổng quan và chức năng của các thành phần như VPC, Subnet, Gateway và Route Table | awsstudygroup.com (Lab 3) |
| Thứ Bảy | 25/04/2026 | Làm Lab 3 phần tạo VPC và Subnet; vừa thực hành vừa đối chiếu tài liệu để hiểu cách chia dải CIDR và phân biệt Public/Private Subnet | awsstudygroup.com (Lab 3) |
| Chủ Nhật | 26/04/2026 | Tiếp tục Lab 3 với Internet Gateway và Route Table, cấu hình định tuyến để Public Subnet có thể ra internet; đọc thêm phần VPC Security | awsstudygroup.com (Lab 3) |
| Thứ Hai | 27/04/2026 | Thiết lập Security Group cho Public và Private; ban đầu còn lẫn giữa inbound và outbound nên phải làm lại nhiều lần và kiểm tra từng rule cho đến khi nắm rõ | awsstudygroup.com (Lab 3) |
| Thứ Ba | 28/04/2026 | Dành thời gian hệ thống lại toàn bộ kiến thức VPC đã học, tự vẽ sơ đồ mạng để ghi nhớ cách các thành phần kết nối với nhau |  |
| Thứ Tư | 29/04/2026 | Thực hành triển khai EC2 trong VPC và cấu hình EC2 Instance Connect Endpoint để truy cập an toàn vào instance trong Private Subnet | awsstudygroup.com (Lab 3) |
| Thứ Năm | 30/04/2026 | Kiểm tra kết nối giữa các EC2 trong VPC, xác nhận Public/Private hoạt động đúng thiết kế; tổng kết phần cơ bản của Module 02 |  |

### Kết quả đạt được tuần 2:
* Hiểu và tự cấu hình được các thành phần chính của VPC: Subnet, Internet Gateway, Route Table và Security Group.
* Phân biệt được Public Subnet với Private Subnet, đồng thời nắm cách định tuyến để đi ra internet.
* Triển khai thành công EC2 trong VPC và sử dụng Instance Connect Endpoint để truy cập instance Private an toàn.
* Vượt qua được giai đoạn nhầm lẫn giữa inbound và outbound của Security Group.
* Có nền tảng mạng vững hơn để bước sang các lab nâng cao ở tuần sau.
