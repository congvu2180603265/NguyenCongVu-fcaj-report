---
title : "Amazon EventBridge"
date : 2026-07-10
weight : 5
chapter : false
pre : " <b> 5.13.5. </b> "
---

#### Xóa Amazon EventBridge

1. Truy cập **Amazon EventBridge Console** → **Buses** → **Rules**. Chọn event bus **default**. Trong tab **Event pattern rules**, chọn rule `playwright-postprocessing-trigger` (Status: **Enabled**), sau đó nhấn nút **Delete**.
![EventBridge 1](/images/5-Workshop/5.13-cleanup/5.13.5-eventbridge/eventbridge1.png?featherlight=false&width=90pc)

2. Hộp thoại **Delete rule?** xuất hiện, xác nhận việc xóa rule `playwright-postprocessing-trigger` và cảnh báo thao tác này sẽ ảnh hưởng đến các resources liên quan. Nhập `delete` vào ô xác nhận và nhấn **Delete**.
![EventBridge 2](/images/5-Workshop/5.13-cleanup/5.13.5-eventbridge/eventbridge2.png?featherlight=false&width=90pc)

3. Sau khi xóa thành công, danh sách **Event pattern rules** trở về trạng thái **No rules** — hiển thị **No rules to display**.
![EventBridge 3](/images/5-Workshop/5.13-cleanup/5.13.5-eventbridge/eventbridge3.png?featherlight=false&width=90pc)
