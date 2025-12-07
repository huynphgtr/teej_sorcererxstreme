---
title : "Chuẩn bị"
weight : 1
chapter : false
pre : " <b> 2.3.1 </b> "
---

### 1. Công Nghệ Sử Dụng 

| Hạng mục | Công nghệ | Chi tiết & Vai trò |
| :--- | :--- | :--- |
| **Ngôn ngữ** | TypeScript (Node.js 20) | Ngôn ngữ chính, cung cấp khả năng kiểm soát kiểu (Type Safety). |
| **Backend Core** | Express.js + `serverless-http` | Framework API quen thuộc, được "đóng gói" để chạy trên Lambda. |
| **Hạ tầng (IaC)** | Serverless Framework V4 | Công cụ chính để định nghĩa và triển khai toàn bộ kiến trúc AWS. |
| **Compute** | AWS Lambda | Xử lý logic nghiệp vụ và chạy mã TypeScript. |
| **API Gateway** | AWS API Gateway | Cổng giao tiếp HTTP đồng bộ cho toàn bộ Backend. |
| **Database** | NeonDB (Serverless PostgreSQL) | Database chính cho dữ liệu quan hệ |
| **ORM** | Prisma | Lớp trừu tượng hóa (Abstraction Layer) giữa code và database. |
| **Bảo mật** | AWS SSM Parameter Store | Nơi lưu trữ an toàn các biến môi trường nhạy cảm. |
| **DevOps** | GitHub Actions | Tự động hóa quy trình CI/CD. |

### 2. Tài Nguyên & Phần Mềm Cần Chuẩn Bị

Để thực hiện workshop, người dùng cần có sẵn các công cụ và tài khoản sau trên máy tính của mình.

#### A. Yêu cầu Tài khoản (Accounts)

* **Tài khoản AWS:** Cần thiết để triển khai các dịch vụ Serverless (Lambda, API Gateway, SSM).
* **Tài khoản NeonDB:** Cần thiết để tạo và lấy chuỗi kết nối (`DATABASE_URL`) cho database PostgreSQL.
* **Tài khoản GitHub:** Cần thiết cho việc lưu trữ mã nguồn và thiết lập CI/CD (GitHub Actions).

#### B. Phần Mềm & Công Cụ Local

* **Node.js (v20+):** Đã được cài đặt và có thể truy cập qua terminal.
* **npm hoặc yarn:** Trình quản lý gói.
* **AWS CLI:** Cần thiết để cấu hình quyền truy cập AWS từ máy local và Serverless Framework.
* **IDE (VS Code):** Môi trường phát triển được khuyến nghị.
* **Postman/Insomnia:** Công cụ cần thiết để kiểm tra các API Endpoint (GET/POST).

#### C. Thiết lập Dự án

* **Serverless Framework CLI:** Cần được cài đặt toàn cục (`npm install -g serverless`).
* **AWS Credentials:** Cấu hình quyền truy cập AWS (User/Role) trên máy local.


