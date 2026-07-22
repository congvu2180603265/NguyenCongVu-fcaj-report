---
title : "EventBridge"
date : 2026-07-10
weight : 11
chapter : false
pre : " <b> 5.11. </b> "
---

#### Cấu hình EventBridge Scheduler để chạy kiểm thử tự động

Trong phần này, bạn sẽ tạo một lịch chạy định kỳ bằng Amazon EventBridge Scheduler. Đến thời gian đã cấu hình, Scheduler gửi một thông điệp JSON vào SQS `playwright-task-queue`; hệ thống phía sau sẽ nhận thông điệp và khởi chạy bài kiểm thử Playwright.

{{% notice note %}}
Hãy đảm bảo hàng đợi SQS `playwright-task-queue` đã được tạo ở phần 5.4. Ví dụ bên dưới chạy lúc **08:00 mỗi ngày** theo múi giờ **Asia/Saigon**. Bạn có thể thay đổi lịch và nội dung thông điệp cho phù hợp.
{{% /notice %}}

---

#### Phần 1: Tạo lịch

**Bước 1:** Mở **Amazon EventBridge Console**. Trong menu bên trái, chọn **Scheduler → Schedules**, sau đó chọn **Create schedule**.

![Mở trang EventBridge Schedules và tạo lịch](/images/5-Workshop/5.11-eventbridge/1-eventbridge-home.png?featherlight=false&width=90pc)

**Bước 2:** Trong **Schedule name**, nhập `playwright-daily-trigger`. Giữ **Schedule group** là `default`.

![Đặt tên cho lịch](/images/5-Workshop/5.11-eventbridge/2-schedule-name.png?featherlight=false&width=90pc)

**Bước 3:** Tại **Schedule pattern**, cấu hình:

- **Occurrence:** Recurring schedule
- **Time zone:** `(UTC+07:00) Asia/Saigon`
- **Schedule type:** Cron-based schedule
- **Cron expression:** `cron(0 8 * * ? *)`

Biểu thức trên chạy vào **08:00 mỗi ngày** theo múi giờ đã chọn. Kiểm tra danh sách **Next 10 trigger dates**, sau đó chọn **Next**.

![Cấu hình lịch cron hằng ngày lúc 08:00](/images/5-Workshop/5.11-eventbridge/3-schedule-cron.png?featherlight=false&width=90pc)

---

#### Phần 2: Chọn SQS làm đích

**Bước 1:** Trong **Select target**, giữ **Templated targets**, sau đó chọn **Amazon SQS – SendMessage**.

![Chọn Amazon SQS làm target](/images/5-Workshop/5.11-eventbridge/4-select-target.png?featherlight=false&width=90pc)

**Bước 2:** Chọn hàng đợi `playwright-task-queue` và nhập **MessageBody**:

```json
{
  "target_url": "https://google.com",
  "test_script": "test-script.js",
  "triggered_by": "schedule"
}
```

Thay `target_url` và `test_script` bằng URL cùng kịch bản kiểm thử thực tế của bạn, rồi chọn **Next**.

![Cấu hình hàng đợi và nội dung thông điệp](/images/5-Workshop/5.11-eventbridge/5-schedule-message.png?featherlight=false&width=90pc)

---

#### Phần 3: Cấu hình quyền và tạo lịch

**Bước 1:** Trong **Settings**, bật **Enable schedule**. Với lịch định kỳ, giữ **Action after schedule completion** là `NONE`. Có thể giữ cấu hình Retry và DLQ mặc định cho bài thực hành.

![Bật lịch và kiểm tra các thiết lập](/images/5-Workshop/5.11-eventbridge/6-schedule-settings.png?featherlight=false&width=90pc)

**Bước 2:** Trong **Permissions**, chọn **Create new role for this schedule**. EventBridge Scheduler sẽ tạo execution role có quyền gửi thông điệp đến SQS đã chọn. Giữ mã hóa mặc định bằng khóa do AWS quản lý, sau đó chọn **Next**.

![Tạo execution role cho Scheduler](/images/5-Workshop/5.11-eventbridge/7-schedule-role.png?featherlight=false&width=90pc)

**Bước 3:** Tại trang **Review and create schedule**, kiểm tra tên lịch, múi giờ, biểu thức cron, target SQS và execution role. Chọn **Create schedule**.

![Kiểm tra cấu hình trước khi tạo lịch](/images/5-Workshop/5.11-eventbridge/8-schedule-review.png?featherlight=false&width=90pc)

**Bước 4:** Xác nhận lịch được tạo với trạng thái **Enabled**. Kiểm tra lại cron expression, target và retry policy trong trang chi tiết.

![Lịch EventBridge đã được tạo và bật](/images/5-Workshop/5.11-eventbridge/9-schedule-created.png?featherlight=false&width=90pc)

---

#### Phần 4: Tạo Event Pattern Rule cho Lambda Post-processing

Ngoài lịch chạy định kỳ ở Phần 1-3 (thuộc mục **Scheduler**), hệ thống còn cần **1 Rule riêng** thuộc mục **Buses → Rules** để tự động trigger Lambda `playwright-postprocessing` ngay khi Fargate Task kết thúc — không phụ thuộc vào lịch chạy hay trigger thủ công.

{{% notice note %}}
"Rules" và "Schedules" là 2 khái niệm khác nhau trong EventBridge. **Schedules** (Phần 1-3) chạy theo thời gian định trước. **Rules** (Phần 4) bắt theo **sự kiện xảy ra** (event pattern) — ở đây là sự kiện ECS Task chuyển sang trạng thái STOPPED, bất kể task đó được trigger thủ công, theo lịch, hay bằng cách nào khác.
{{% /notice %}}

**Bước 1:** Mở **Amazon EventBridge Console**, menu bên trái chọn mục **Buses → Rules**, ở tab **Event pattern rules** bấm nút cam **Create rule**.

![Mở trang Rules và bấm Create rule](/images/5-Workshop/5.11-eventbridge/10-open-rules-create.png?featherlight=false&width=90pc)

**Bước 2:** Ở trang **Create rule**, mục **Builder mode** phía trên cùng, giữ nguyên **Enhanced builder** (Visual drag-and-drop builder) — đây là chế độ mặc định.

**Bước 3:** Chuyển sang tab **Configure** (cạnh tab **Build** ở giữa màn hình), đặt **Name** = `playwright-postprocessing-trigger`. Event bus ở tab **Build** giữ mặc định `default`.

![Đặt tên rule ở tab Configure](/images/5-Workshop/5.11-eventbridge/11-configure-name.png?featherlight=false&width=90pc)

**Bước 4:** Quay lại tab **Build**. Cuộn xuống phần **Triggering events** bên dưới canvas, khung **Event pattern (filter)** bên phải đã sẵn sàng để nhập sẵn (đang trống, dòng 1, có thể thấy lỗi tạm thời **"JSON is invalid: Unexpected end of JSON input"** — bình thường vì chưa nhập gì). Bấm vào khung này và dán JSON pattern sau vào:

```json
{
  "source": ["aws.ecs"],
  "detail-type": ["ECS Task State Change"],
  "detail": {
    "lastStatus": ["STOPPED"],
    "clusterArn": ["arn:aws:ecs:ap-southeast-1:<account-id>:cluster/playwright-cluster"]
  }
}
```

Thay `<account-id>` bằng Account ID thật của bạn.

![Dán event pattern JSON vào khung Event pattern (filter)](/images/5-Workshop/5.11-eventbridge/12-paste-json-pattern.png?featherlight=false&width=90pc)

**Bước 5:** Ở cột bên trái, bấm tab **Targets** (cạnh tab **Events**), ở khung tìm kiếm target chọn **AWS service** → **Lambda function** → chọn function `playwright-postprocessing`. Kéo vào khung **Targets** bên phải canvas (hoặc bấm chọn trực tiếp tùy giao diện).

![Chọn Lambda playwright-postprocessing làm Target](/images/5-Workshop/5.11-eventbridge/13-select-lambda-target.png?featherlight=false&width=90pc)

**Bước 6:** Sau khi cả 2 khung **Triggering Events** và **Targets** đều hết cảnh báo đỏ, bấm nút cam **Create** ở góc trên phải màn hình.

**Bước 7:** Xác nhận rule `playwright-postprocessing-trigger` xuất hiện trong danh sách **Event pattern rules**, trạng thái **Enabled**, Type = **Standard**, Event bus = **default**.

![Rule playwright-postprocessing-trigger đã được tạo, trạng thái Enabled](/images/5-Workshop/5.11-eventbridge/14-rule-created.png?featherlight=false&width=90pc)

{{% notice warning %}}
AWS Console hiện banner cảnh báo **"We have moved Scheduled rules in your account to 'Scheduler' section"** — đây chỉ là thông báo di chuyển tính năng Schedule cũ (legacy) sang mục Scheduler mới, không ảnh hưởng đến Rule dạng Event pattern bạn vừa tạo ở đây.
{{% /notice %}}

---

#### Kiểm tra

- Xác nhận lịch Scheduler có trạng thái **Enabled** và lần chạy kế tiếp đúng với múi giờ `Asia/Saigon`.
- Đến thời điểm chạy, mở SQS và theo dõi `playwright-task-queue` để xác nhận thông điệp được gửi vào hàng đợi.
- Xác nhận rule `playwright-postprocessing-trigger` ở trạng thái **Enabled**.
- Sau khi 1 Task Fargate chạy xong và chuyển sang STOPPED, kiểm tra Lambda `playwright-postprocessing` có tự động được trigger (xem CloudWatch Logs của Lambda này để xác nhận).
- Kiểm tra CloudWatch Logs của thành phần xử lý SQS/ECS để xác nhận bài kiểm thử được khởi chạy với đúng `target_url` và `test_script`.
- Nếu cần thử ngay, tạm thời đổi lịch sang thời điểm gần nhất, kiểm tra kết quả rồi khôi phục lịch hằng ngày.

{{% notice warning %}}
EventBridge Scheduler dùng biểu thức cron gồm 6 trường và không cho phép đồng thời đặt giá trị cụ thể cho cả **day-of-month** và **day-of-week**. Dùng `?` cho một trong hai trường này.
{{% /notice %}}

---

Tiếp theo, chúng ta sẽ chuyển sang **[5.12. Kiểm thử End-to-End](../5.12-end-to-end-test/)** để xác minh toàn bộ luồng hệ thống.
