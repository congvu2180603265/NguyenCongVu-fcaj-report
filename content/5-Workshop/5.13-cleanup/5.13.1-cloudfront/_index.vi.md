---
title : "CloudFront"
date : 2026-07-10
weight : 1
chapter : false
pre : " <b> 5.13.1. </b> "
---

#### Xóa CloudFront

Bạn không thể xóa trực tiếp một CloudFront Distribution đang hoạt động. Trước tiên cần hủy Free plan và vô hiệu hóa (Disable) distribution, sau đó mới có thể xóa.

1. Truy cập **CloudFront Console** → **Distributions**. Tích chọn distribution `E3APVVDEOB1F3Z` (`playwright-cloudfront-123`), sau đó nhấn nút **Disable** trên thanh công cụ. Hộp thoại **Disable distribution?** hiện ra xác nhận → nhấn **Disable** để tiến hành vô hiệu hóa.
![CloudFront 1](/images/5-Workshop/5.13-cleanup/5.13.1-cloudfront/1.png?featherlight=false&width=90pc)

2. Nhấp vào ID distribution để vào trang chi tiết. Tại mục **Billing**, trạng thái đang là **Free plan ($0/month)** → nhấn nút **Manage plan** để tiến hành hủy gói này.
![CloudFront 2](/images/5-Workshop/5.13-cleanup/5.13.1-cloudfront/2.png?featherlight=false&width=90pc)

3. Hộp thoại **Review change: Free to Pay-as-you-go** hiện ra, xác nhận việc chuyển từ Free plan sang Pay-as-you-go. Nhấn **Cancel plan** để hủy gói Free plan.
![CloudFront 3](/images/5-Workshop/5.13-cleanup/5.13.1-cloudfront/3.png?featherlight=false&width=90pc)

4. Sau khi hủy thành công, hệ thống trả về trang chi tiết distribution với mục **Billing** đã đổi thành **Pay-as-you-go** và trạng thái **Deploying** đang được cập nhật.
![CloudFront 4](/images/5-Workshop/5.13-cleanup/5.13.1-cloudfront/4.png?featherlight=false&width=90pc)

5. Quay lại danh sách **Distributions**, tích chọn distribution có Status là **Disabled**, rồi nhấn nút **Delete**. Hộp thoại **Delete distribution?** hiện ra → nhấn **Delete** để xác nhận.
![CloudFront 5](/images/5-Workshop/5.13-cleanup/5.13.1-cloudfront/5.png?featherlight=false&width=90pc)

6. Xóa thành công, hệ thống hiển thị thông báo màu xanh **"Deleted distribution: E3APVVDEOB1F3Z."** và danh sách trở về trạng thái **No distributions**.
![CloudFront 6](/images/5-Workshop/5.13-cleanup/5.13.1-cloudfront/6.png?featherlight=false&width=90pc)
