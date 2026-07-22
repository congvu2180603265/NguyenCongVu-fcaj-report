---
title : "Amazon VPC"
date : 2026-07-10
weight : 13
chapter : false
pre : " <b> 5.13.13. </b> "
---

#### Xóa Amazon VPC

Trước khi xóa VPC, bạn phải xóa 4 VPC Endpoints được gắn với nó. **Không xóa Default VPC** — chỉ xóa `playwright-vpc`.

1. Truy cập **VPC Console** → **Endpoints**. Trong danh sách **Endpoints (4/4)**, chọn tất cả 4 endpoints: `playwright-endpoint-s3` (Gateway), `playwright-endpoint-ecr-api` (Interface), `playwright-endpoint-ecr-dkr` (Interface), và `playwright-endpoint-logs` (Interface). Sau đó chọn **Actions** → **Delete VPC endpoints**.
![VPC 1](/images/5-Workshop/5.13-cleanup/5.13.13-vpc/vpc3.png?featherlight=false&width=90pc)

2. Hộp thoại **Delete endpoints** xuất hiện, liệt kê tất cả 4 endpoints sẽ bị xóa vĩnh viễn. Nhập `delete` vào ô xác nhận và nhấn **Delete**.
![VPC 2](/images/5-Workshop/5.13-cleanup/5.13.13-vpc/vpc4.png?featherlight=false&width=90pc)

3. Hệ thống hiển thị banner xanh **"Successfully deleted endpoints"** kèm các ID của 4 endpoints đã xóa. Các Interface endpoints còn lại hiển thị status **Deleting**.
![VPC 3](/images/5-Workshop/5.13-cleanup/5.13.13-vpc/vpc5.png?featherlight=false&width=90pc)

4. Sau khi tất cả endpoints được xóa, danh sách **Endpoints** hiển thị **No endpoint found**.
![VPC 4](/images/5-Workshop/5.13-cleanup/5.13.13-vpc/vpc6.png?featherlight=false&width=90pc)

5. Bây giờ, điều hướng đến **VPC Console** → **Your VPCs**. Xác định đúng `playwright-vpc` cần xóa (VPC không phải Default VPC). Bấm chọn vào VPC có tên `playwright-vpc` và xác nhận State của nó là **Available**.
![VPC 5](/images/5-Workshop/5.13-cleanup/5.13.13-vpc/vpc2.png?featherlight=false&width=90pc)

6. Với `playwright-vpc` đang được chọn, nhấn **Actions** → **Delete VPC**. Hộp thoại **Delete VPC** xuất hiện hiển thị VPC `playwright-vpc` (State: Available) sẽ bị xóa, cùng với 8 tài nguyên liên quan cũng sẽ bị xóa (bao gồm `playwright-igw`, `playwright-public-rtb`, `playwright-private-rtb`, và các security groups). Nhập `delete` vào ô xác nhận và nhấn **Delete**.
![VPC 6](/images/5-Workshop/5.13-cleanup/5.13.13-vpc/vpc7.png?featherlight=false&width=90pc)

7. Sau khi thành công, hệ thống hiển thị banner xanh **"You successfully deleted vpc-0b31d28ff732f70c9 / playwright-vpc and 8 other resources."** Danh sách **Your VPCs** hiển thị **No VPCs found in this Region**.
![VPC 7](/images/5-Workshop/5.13-cleanup/5.13.13-vpc/vpc8.png?featherlight=false&width=90pc)
