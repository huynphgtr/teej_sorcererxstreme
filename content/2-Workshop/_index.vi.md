---
title: "Workshop"
weight: 1
chapter: false
pre: " <b> 2. </b> "
--- 
# SorcererXtreme: Xây dựng Nền tảng luận giải dựa trên AI trên AWS

### Tổng quan

SorcererXtreme AI là một nền tảng hướng dẫn siêu hình tiên phong, tận dụng AI và kiến trúc Serverless của AWS để cung cấp các bài đọc cá nhân hóa, có cơ sở và đáng tin cậy về Chiêm tinh học, Tarot, Tử vi và Số học.

### 1. Phát triển Frontend 

Phần này tập trung vào việc xây dựng giao diện người dùng React/Next.js và tích hợp với AWS Amplify.

| Mục tiêu | Công nghệ & Khái niệm | Sản phẩm Đầu ra |
| :--- | :--- | :--- |
| **Giao diện & UX** | Xây dựng các component chính (Chat UI, Profile Settings, Payment Gateways). | Giao diện người dùng trực quan, responsive cho các dịch vụ: Tarot Reading, Astrology Chart. |
| **Xác thực** | Tích hợp AWS Cognito và Amplify Authenticator vào Frontend. | Hệ thống đăng nhập/đăng ký hoạt động đầy đủ. |
| **Tích hợp Backend** | Viết các hàm Client-side API calls (`axios`) để gọi đến API Gateway Endpoint (Backend). | Các hàm Fetch data/Post requests hoạt động, hiển thị dữ liệu (Response) từ Lambda. |


### 2. Phát triển Backend 

Đây là phần cốt lõi của kiến trúc Serverless, tập trung vào logic nghiệp vụ và tối ưu hóa hiệu suất.

| Mục tiêu | Công nghệ & Khái niệm | Sản phẩm Đầu ra |
| :--- | :--- | :--- |
| **Lớp API & DB** | Thiết lập Express.js và **`serverless-http`** trên AWS Lambda. Cấu hình Prisma và kết nối an toàn với NeonDB. | Các hàm Lambda cơ bản hoạt động: `UserAPI` (CRUD hồ sơ) và `ReminderService`. |
| **Kiến trúc Asynchronous** | Xây dựng luồng Reminder Service bằng EventBridge Scheduler và Amazon SES. | Logic `findUsersToRemind` hoạt động và tự động gửi email thông báo.  |
| **Tối ưu hóa** | Tối ưu gói triển khai Serverless/Prisma, và thiết lập IAM Roles tối thiểu. | Mã nguồn Backend được triển khai tự động qua GitHub Actions (CI/CD). |


### 4. Phát triển AI 
Phần quan trọng nhất, nơi logic RAG được thiết lập để cung cấp nội dung thông minh.

| Mục tiêu | Công nghệ & Khái niệm | Sản phẩm Đầu ra |
| :--- | :--- | :--- |
| **Lõi RAG** | Hiểu và thiết lập luồng Retrieval-Augmented Generation (RAG). | Kiến trúc RAG hoạt động. |
| **Bedrock & Embeddings** | Sử dụng Amazon Bedrock để tạo Vector Embeddings từ câu hỏi của người dùng và gọi các LLMs. | Hàm Lambda (`ChatbotAPI`) gọi thành công Bedrock để tạo sinh câu trả lời. |
| **Truy xuất Dữ liệu** | Sử dụng Pinecone làm Vector Database để lưu trữ và truy xuất các Chunk kiến thức từ kho tri thức RAG (lưu trữ trong S3). | Quá trình tìm kiếm ngữ cảnh (context retrieval) và truyền ngữ cảnh đó vào Prompt để tạo sinh câu trả lời chính xác. |

### Nội dung

1.  [Tổng quan về Workshop](5.1-Workshop-overview)
2.  [Phát triển Frontend](5.2-Frontend-Development/)
3.  [Phát triển Backend](5.3-Backend-Development/)
4.  [Phát triển AI](5.4-AI-Development/)