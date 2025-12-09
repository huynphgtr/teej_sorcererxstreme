---
title : "Phần phát triển Backend"
weight : 3 
chapter : false
pre : " <b> 2.3. </b> "
---

## Xây dựng và Tự động hóa với Backend Serverless (Serverless V4)

Bài hướng dẫn này là tài liệu tổng hợp, chi tiết toàn bộ quy trình phát triển Backend cho dự án SorcererXtreme AI, từ việc thiết lập môi trường phát triển cục bộ cho đến khi hoàn thành quy trình triển khai tự động (CI/CD) lên AWS.

### 1. Bối cảnh & Thách thức Kỹ thuật

Trong quá trình phát triển các ứng dụng Serverless hiệu suất cao, chúng ta phải đối mặt với nhiều rào cản kỹ thuật phức tạp:

* **Chuyển đổi Framework:** Việc chuyển đổi từ Framework HTTP truyền thống như Express.js sang môi trường phi kết nối (stateless) của AWS Lambda đòi hỏi sử dụng `serverless-http` và thay đổi cấu trúc mã nguồn.
* **Quản lý Cơ sở dữ liệu:** Sử dụng ORM phức tạp như Prisma trên nền tảng Serverless yêu cầu kỹ thuật tối ưu hóa gói triển khai (deployment package size) dưới 250MB và phải tải đúng Prisma Binary (rhel-openssl-3.0.x) cho môi trường Linux của Lambda.
* **Bảo mật:** Đảm bảo các chuỗi kết nối nhạy cảm không bao giờ được lưu trữ trong mã nguồn, mà phải được quản lý an toàn bằng AWS SSM Parameter Store.

### 2. Giá trị Cốt lõi của Kiến trúc Được xây dựng

Kiến trúc Backend của chúng ta được thiết kế để giải quyết những thách thức trên, mang lại các lợi ích then chốt:

* **Tối ưu hóa Chi phí và Hiệu suất:** Sử dụng AWS Lambda và Serverless Framework V4 đảm bảo bạn chỉ trả tiền cho thời gian code thực sự chạy. Việc tối ưu hóa gói Prisma giúp giảm đáng kể thời gian khởi động (Cold Start).
* **Tự động hóa hoàn toàn (CI/CD):** Xây dựng quy trình GitHub Actions giúp tự động hóa toàn bộ vòng lặp phát triển: Code -> Push -> Build -> Deploy, loại bỏ hoàn toàn các bước triển khai thủ công và giảm thiểu lỗi do con người.
* **Tính linh hoạt Dữ liệu:** Thiết lập kết nối ổn định và an toàn với Database bên ngoài AWS , minh chứng cho khả năng tích hợp linh hoạt trong các dự án thực tế.

Bài hướng dẫn này sẽ là bản đồ chi tiết từng bước, giúp bạn làm chủ toàn bộ quy trình phát triển Backend Serverless hiện đại.

1. [Chuẩn bị](2.3.1-Prepare/)
2. [Thiết lập môi trường](2.3.2-Set-up/)
3. [Cấu hình Serverless Framework](2.3.3-Configure-serverless/)
4. [Lưu khóa bảo mật](2.3.4-AWS-SSM/)
5. [Tự động hóa CI/CD](2.3.5-CICD/)
6. [Kết nối microservices](2.3.6-Microservice/)
7. [Thiết lập email](2.3.7-Email/)
8. [Tổng kết quy trình phát triển](2.3.8-Summary/)

