---
title : "Tổng kết quy trình làm việc"
weight : 8
chapter : false
pre : " <b> 2.3.8 </b> "
---

Quy trình làm việc được thiết kế để tận dụng tốc độ phát triển cục bộ và tính an toàn, tự động của kiến trúc Serverless.

| Bước | Hoạt động | Môi trường | Vai trò và Ghi chú |
| :---: | :--- | :--- | :--- |
| **1. Phát triển Code** | Code logic, sửa đổi API (TypeScript/Express). | Máy Local | Sử dụng VS Code và các thư viện Node.js. |
| **2. Kiểm thử Cục bộ** | Test đồng bộ API. | `sls offline start` | Giả lập môi trường Lambda/API Gateway. Kết nối trực tiếp với NeonDB qua file `.env` local. |
| **3. Cập nhật Schema DB** | **Nếu sửa đổi Schema** (`schema.prisma`). | `npx prisma migrate dev` | **Tạo Migration** và áp dụng các thay đổi. |
| **4. Commit & Đẩy Code** | Commit code và đẩy lên kho chứa. | `git push origin main` | Kích hoạt luồng tự động hóa CI/CD. |
| **5. CI/CD Tự động** | Triển khai lên Cloud. | GitHub Actions | Tự động: Build -> Prisma Generate -> Đăng nhập AWS/SSM (lấy Key) -> Deplo lên AWS Lambda. |
| **6. Giám sát Lỗi** | Kiểm tra Log thời gian thực. | `sls logs -f api -t` | Dùng lệnh Serverless Framework để xem CloudWatch Logs ngay lập tức và debug lỗi trong môi trường Production/Staging. |

### Ghi chú Quan trọng về Prisma:

1.  **Dùng `migrate dev`:** Thay vì `db push` (chỉ dùng cho non-production/test), bạn nên dùng `npx prisma migrate dev` để tạo lịch sử thay đổi (migration files) và áp dụng lên NeonDB.
2.  **Generate trên CI/CD:** Việc chạy `npx prisma generate` trong GitHub Actions là **bắt buộc** để tải xuống các binaries (rhel-openssl-3.0.x) cần thiết cho môi trường Linux của AWS Lambda.



