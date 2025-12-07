---
title : "Thiết lập Email"
weight : 7
chapter : false
pre : " <b> 2.3.7 </b> "
---

Để đảm bảo email của dự án được gửi đi với độ tin cậy cao nhất và tránh bị các nhà cung cấp dịch vụ email (như Gmail, Outlook) đánh dấu là Spam, việc cấu hình bảo mật tên miền là bắt buộc.

### 1. Cấu hình Cơ bản và Bảo mật Danh tính 

| Bước | Hoạt động | Mục đích |
| :---: | :--- | :--- |
| **1. Mua Domain** | Sử dụng domain riêng (`.com`, `.xyz`) thay vì domain dùng chung. | **Uy tín:** Xây dựng danh tiếng gửi email riêng biệt, tránh bị ảnh hưởng bởi người gửi xấu khác. |
| **2. Verify Domain trong AWS SES** | Truy cập SES -> Verified Identities -> Create Identity (Chọn Domain).  | Chứng minh Quyền sở hữu: Cho phép AWS SES quản lý việc gửi email thay mặt cho tên miền của bạn. |
| **3. Cấu hình DNS (Bảo mật Email)** | Thêm các bản ghi CNAME (DKIM), TXT (SPF), và TXT (DMARC). | Quan trọng: Các bản ghi này xác minh nguồn gửi (DKIM/SPF) và hướng dẫn máy chủ nhận xử lý email không hợp lệ (DMARC), ngăn chặn email rơi vào Spam. |

#### Chi tiết các Bản ghi DNS bắt buộc:

* **DKIM (CNAME):** Thêm 3 bản ghi CNAME mà AWS SES cung cấp. *Mục đích: Chữ ký số.*
* **SPF (TXT):** Thêm bản ghi `TXT` với nội dung: `v=spf1 include:amazonses.com ~all`. *Mục đích: Xác định AWS SES là máy chủ được ủy quyền để gửi mail.*
* **DMARC (TXT):** Thêm bản ghi `TXT` (thường dưới dạng `_dmarc`) với nội dung: `v=DMARC1; p=none;`. *Mục đích: Thiết lập chính sách báo cáo và xử lý email giả mạo.*

### 2. Thiết lập Luồng Gửi và Vượt Sandbox

Sau khi tên miền đã được xác minh (Verification Status: Verified), bạn cần thiết lập luồng vận hành và xin quyền gửi thực tế.

| Bước | Dịch vụ tương tác | Hoạt động |
| :---: | :--- | :--- |
| **1. Kích hoạt Luồng Async** | EventBridge Scheduler -> Lambda (TriggerReminder) | Luồng bất đồng bộ bắt đầu theo lịch trình đã định. |
| **2. Gửi Yêu cầu SES** | Lambda (TriggerReminder) ->  Amazon SES API | Hàm Lambda (đã được cấp quyền IAM Role) gọi trực tiếp SES API (ví dụ: `SendEmailCommand`) để gửi email cá nhân hóa cho từng người dùng. |
| **3. Request Production Access** | AWS Support Center | Gửi ticket yêu cầu AWS nâng cấp tài khoản của bạn khỏi chế độ Sandbox để bạn có thể gửi email đến bất kỳ địa chỉ nào chưa được xác minh. |