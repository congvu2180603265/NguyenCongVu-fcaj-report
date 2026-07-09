---
title: "AWS Transform – Khi AI Tự Động Dọn Nợ Kỹ Thuật Cho Cả Ngàn Repository"
date: 2026-06-20
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

AWS vừa ra mắt tính năng mới trong AWS Transform: Continuous Modernization (đang preview) — về cơ bản là một công cụ tự động quét, phát hiện và sửa technical debt trên toàn bộ codebase của tổ chức, không cần con người làm thủ công từng repo.

### Vấn Đề Nó Giải Quyết

Các công ty thường tốn đến 30% ngân sách IT chỉ để duy trì hệ thống cũ. Thực trạng phổ biến là dùng nhiều tool rời rạc: tool này phát hiện dependency cũ, tool kia check vulnerability, tool khác check code quality — nhưng không có gì gắn kết toàn bộ lại và tự động sửa ở quy mô lớn.

Kết quả: team engineer bị ngốn thời gian vào việc dọn dẹp thay vì xây tính năng mới. Và ironically, AI coding agent càng làm tình trạng này tệ hơn — code sinh ra nhanh hơn, nhưng debt cũng tích lũy nhanh hơn.

### Nó Hoạt Động Như Thế Nào

Có 2 chế độ:

* **Continuous mode** — quét liên tục toàn bộ repo theo các policy định sẵn (hoặc policy tự định nghĩa của tổ chức). Khi phát hiện repo nào lạc hậu so với baseline, nó tự tạo Pull Request để sửa và notify team đó. Team chỉ cần review và merge.
* **Campaign mode** — dành cho các dự án modernization lớn hơn, ví dụ migrate framework hoặc nâng major version trên hàng trăm app cùng lúc.

Out-of-the-box nó đã hỗ trợ: nâng Java version, upgrade Node.js, migrate AWS SDK, cập nhật Lambda runtime trước khi hết hạn support.

### Điểm Thú Vị

Nó tích hợp với AWS Security Agent — tức là lỗ hổng bảo mật ở tầng source code cũng được đưa vào cùng một pipeline phát hiện và sửa, không phải một tool riêng biệt nữa.

Ngoài ra có thể dùng qua MCP — nghĩa là tích hợp được vào các coding agent hiện có của tổ chức.

Link bài viết gốc: [aws.amazon.com/blogs/aws/proactively-reduce-tech-debt-autonomously-with-aws-transform-continuous-modernization-preview](https://aws.amazon.com/vi/blogs/aws/proactively-reduce-tech-debt-autonomously-with-aws-transform-continuous-modernization-preview)

{{% notice tip %}}
Ảnh chụp bài đăng trên nhóm Facebook AWS Study Group để tại đây.
{{% /notice %}}
![Facebook post screenshot](/images/anhblog3.png)

#AWS #AWSTransform #TechnicalDebt #DevOps #AIAgent #CloudModernization