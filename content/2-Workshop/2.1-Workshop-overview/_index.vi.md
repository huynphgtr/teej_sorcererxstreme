---
title : "Giới thiệu"
weight : 1 
chapter : false
pre : " <b> 2.1. </b> "
---

## SorcererXtreme AI: Xây dựng Nền tảng Luận giải Dựa trên AI trên AWS

Mục đích cốt lõi của dự án này, đối với một workshop dành cho nhà phát triển, là để minh họa cách xây dựng một ứng dụng đa diện, có khả năng mở rộng, tối ưu chi phí, có khả năng xử lý các luồng dữ liệu phức tạp hoàn toàn trong môi trường đám mây.

### Vấn đề được giải quyết & Giá trị Kỹ thuật

  * **Thách thức:** Xây dựng một nền tảng kết hợp nhu cầu tính toán chính xác với sự sáng tạo ngôn ngữ của AI, đồng thời đảm bảo tất cả nội dung đều có thể kiểm chứng và có cơ sở. Các giải pháp dựa trên máy chủ truyền thống thường gặp khó khăn với khả năng mở rộng động cần thiết cho các khối lượng công việc đa dạng như vậy.
  * **Giải pháp Kỹ thuật:** Chúng tôi giải quyết vấn đề này bằng cách triển khai Lõi Retrieval-Augmented Generation (RAG) sử dụng Amazon Bedrock và Pinecone. Thiết kế này cho phép AI tạo ra các câu trả lời đã được xác minh dựa trên cơ sở kiến thức chuyên biệt, biến hướng dẫn mang tính suy đoán thành những hiểu biết sâu sắc có thể hành động.

### Điểm nổi bật Kỹ thuật Chính

Dự án này đóng vai trò là một nghiên cứu điển hình thiết yếu để tích hợp các dịch vụ AWS quan trọng sau:

1.  **Điện toán Serverless (phi máy chủ):** Chúng tôi sử dụng AWS Lambda làm toàn bộ Backend, loại bỏ chi phí quản lý máy chủ và tối ưu hóa đáng kể chi phí.
2.  **Triển khai Hiện đại (Lưu trữ Frontend):** Triển khai ứng dụng Next.js trên AWS Amplify cung cấp khả năng lưu trữ và CI/CD hợp lý cho Frontend.
3.  **Luồng Bất đồng bộ Bền vững (Async):** Chúng tôi đã xây dựng một hệ thống nhắc nhở tự động đáng tin cậy bằng cách sử dụng EventBridge -> Lambda -> SES. Mô hình này đảm bảo việc gửi hàng loạt tin nhắn mạnh mẽ, có khả năng mở rộng mà không làm quá tải API cốt lõi.
4.  **Tính bền vững của Dữ liệu và Vector:** Chúng tôi quản lý dữ liệu quan hệ phức tạp bên ngoài bằng cách sử dụng NeonDB  đồng thời sử dụng Cơ sở dữ liệu Vector chuyên dụng (Pinecone) cho lớp truy xuất RAG tốc độ cao.
5.  **Bảo mật và DevOps:** AWS Parameter Store quản lý tất cả các khóa nhạy cảm và toàn bộ cơ sở hạ tầng được triển khai bằng Serverless Framework được điều khiển bởi GitHub Actions (CI/CD).

#### Nội dung

1. [Tổng quan workshop](2.1-Workshop-overview)
2. [Phần phát trFrontend](2.2-Frontend-Development/)
3. [Backend Development](2.3-Backend-Development/)
4. [AI Development](2.4-AI-Development/)