---
title: "Chạy thử (End-to-End Test)"
date: 2026-07-10
weight: 12
chapter: false
pre: " <b> 5.12. </b> "
---

Sau khi đã triển khai toàn bộ hạ tầng backend và frontend, bây giờ chúng ta sẽ tiến hành kiểm thử toàn bộ luồng hệ thống thông qua giao diện WebUI.

### 1. Truy cập WebUI Dashboard

Mở trình duyệt và truy cập vào đường dẫn WebUI của bạn (từ CloudFront hoặc S3 static website). Bạn sẽ thấy giao diện Admin Dashboard tổng quan thống kê số lần chạy, tỷ lệ Pass/Fail và xu hướng trong tuần.

![Dashboard](/images/5-Workshop/5.12-end-to-end-test/1-dashboard.jpg)

### 2. Quản lý kịch bản kiểm thử (Test Suites)

Bước tiếp theo là tải lên kịch bản kiểm thử Playwright.
- Ở menu bên trái, chọn **Test Suites**.
- Nhấn vào nút **+ Thêm Test Suite** ở góc trên bên phải.

![Quản lý Test Suites](/images/5-Workshop/5.12-end-to-end-test/2-test-suites.jpg)

- Trong cửa sổ hiện ra, nhập các thông tin:
  - **Tên file:** Tên file hiển thị (ví dụ: `login.spec.js`)
  - **Website:** Tên website mục tiêu
  - **Mô tả:** Mô tả ngắn gọn về kịch bản
- Nhấn **Choose File** để chọn file `.spec.js` từ máy tính của bạn và nhấn **Upload**. File này sẽ được tải lên Amazon S3.

![Thêm Test Suite](/images/5-Workshop/5.12-end-to-end-test/3-add-test-suite.jpg)

### 3. Kích hoạt kiểm thử thủ công

Bây giờ chúng ta sẽ kích hoạt chạy thử nghiệm.
- Ở menu bên trái, chọn **Chạy thủ công**.

![Chạy thủ công](/images/5-Workshop/5.12-end-to-end-test/4-manual-trigger.jpg)

- Điền các thông số để khởi tạo phiên kiểm thử:
  - **URL Website cần kiểm thử:** Điền URL trang web (ví dụ: `https://the-internet.herokuapp.com/`)
  - **Môi trường:** Chọn môi trường (ví dụ: `Staging`)
  - **Kịch bản kiểm thử:** Chọn file test bạn vừa upload ở bước 2.
- Nhấn nút **Kích hoạt kiểm thử ngay**.

![Điền thông tin chạy thủ công](/images/5-Workshop/5.12-end-to-end-test/5-manual-trigger-filled.jpg)

Hệ thống sẽ ngay lập tức gửi thông điệp vào hàng đợi SQS và ECS Fargate sẽ tiến hành kéo Docker Image về để thực thi kịch bản.

![Khởi tạo hệ thống](/images/5-Workshop/5.12-end-to-end-test/6-initializing.jpg)

Sau khi xử lý thành công bước gửi tin, màn hình sẽ thông báo **"Đã gửi yêu cầu vào hàng đợi!"**. Phiên kiểm thử lúc này đang chạy ngầm trên ECS.

![Gửi yêu cầu thành công](/images/5-Workshop/5.12-end-to-end-test/7-queued.jpg)

### 4. Xem lịch sử và chi tiết kiểm thử

Để theo dõi kết quả của các phiên chạy:
- Chuyển sang menu **Lịch sử**. Tại đây, bạn sẽ thấy danh sách các lần chạy cùng với trạng thái (`Pass` hoặc `Fail`), thời lượng chạy, và thông tin người kích hoạt. Toàn bộ dữ liệu này được lưu trữ trong DynamoDB.

![Lịch sử kiểm thử](/images/5-Workshop/5.12-end-to-end-test/8-history.jpg)

- Nhấn nút **Chi tiết** trên một dòng để xem log chi tiết. Giao diện này sẽ hiển thị toàn bộ log thực thi trực tiếp từ **CloudWatch Logs**, cho phép bạn xem tiến trình chạy của Playwright test.

![Chi tiết Test](/images/5-Workshop/5.12-end-to-end-test/9-history-detail.jpg)

### 5. Nhận kết quả và báo cáo qua Email

Sau khi test hoàn tất, Lambda Post-processing sẽ tự động lấy kết quả, tạo báo cáo tổng hợp với AI và gửi qua Email thông qua Amazon SES.

- Kiểm tra hộp thư đến của bạn, bạn sẽ nhận được một email chứa tóm tắt kết quả (Pass/Fail) và phần đánh giá/tóm tắt bằng AI (Generative AI summary).

![Email báo cáo](/images/5-Workshop/5.12-end-to-end-test/10-email-report.jpg)

- Trong email, hãy click vào link **"Xem báo cáo chi tiết"**. Đường dẫn này sẽ mở ra trang báo cáo chi tiết dạng HTML được sinh ra bởi Playwright và lưu trữ trên S3 report bucket.

![HTML Report](/images/5-Workshop/5.12-end-to-end-test/11-html-report.jpg)

### 6. Các tính năng cấu hình khác

- **Lịch kiểm thử:** Bạn cũng có thể thiết lập lịch chạy tự động cho các kịch bản định kỳ (chạy vào khung giờ cố định) thông qua Amazon EventBridge tại menu **Lịch kiểm thử**.
  ![Lịch kiểm thử](/images/5-Workshop/5.12-end-to-end-test/12-schedule.jpg)

- **Cấu hình Email:** Quản lý danh sách các email nhận thông báo kết quả tại menu **Cấu hình Email**.
  ![Cấu hình Email](/images/5-Workshop/5.12-end-to-end-test/13-email-config.jpg)


Chúc mừng! Bạn đã cấu hình và chạy thành công toàn bộ kiến trúc Serverless Playwright Automation Framework trên AWS.

---

Cuối cùng, hãy chuyển sang **[5.13. Dọn dẹp tài nguyên](../5.13-cleanup/)** để xóa toàn bộ tài nguyên AWS và tránh phát sinh chi phí.
