---
title : "Amazon ECS"
date : 2026-07-10
weight : 6
chapter : false
pre : " <b> 5.13.6. </b> "
---

#### Xóa Amazon ECS

Trước khi xóa ECS cluster, bạn cần đảm bảo không còn service nào đang chạy bên trong, sau đó xóa task definition.

1. Truy cập **Amazon ECS Console** → **Clusters** → nhấn vào `playwright-cluster`. Trên tab **Services**, xác nhận có **0 active** services (ECS service đã được dừng trước đó). Trong trang chi tiết cluster, nhấn **Actions** → **Delete cluster**. Hộp thoại **Delete cluster playwright-cluster** xuất hiện. Nhập `delete playwright-cluster` vào ô xác nhận và nhấn **Delete**.
![ECS 1](/images/5-Workshop/5.13-cleanup/5.13.6-ecs/ecs1.png?featherlight=false&width=90pc)

2. Hộp thoại tiến trình **Delete cluster playwright-cluster** hiển thị: **Service deletion** — No services to delete; **Container instance deregistration** — No container instances to deregister; **Cluster and stack deletion** — Successfully deleted `playwright-cluster`. Nhấn **Close** để đóng.
![ECS 2](/images/5-Workshop/5.13-cleanup/5.13.6-ecs/ecs2.png?featherlight=false&width=90pc)

3. Sau khi đóng hộp thoại, trạng thái cluster chuyển sang **Inactive**. Tiếp theo, điều hướng đến **Task definitions** ở sidebar bên trái. Chọn task definition `playwright-task-def` (Status: **Active**), sau đó chọn **Actions** → **Deregister**.
![ECS 3](/images/5-Workshop/5.13-cleanup/5.13.6-ecs/esc3.png?featherlight=false&width=90pc)

4. Trên trang **Task definitions**, nhấn vào task definition `playwright-task-def`. Chọn tất cả các revision, sau đó chọn **Actions** → **Delete**.
![ECS 4](/images/5-Workshop/5.13-cleanup/5.13.6-ecs/esc4.png?featherlight=false&width=90pc)

5. Sau khi xóa thành công, danh sách **Task definitions (0)** với Filter status **Inactive** hiển thị **No task definitions**.
![ECS 5](/images/5-Workshop/5.13-cleanup/5.13.6-ecs/esc5.png?featherlight=false&width=90pc)
