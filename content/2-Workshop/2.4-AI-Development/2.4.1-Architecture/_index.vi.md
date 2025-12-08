---
title : "Kiến trúc & Công nghệ"
weight : 1
chapter : false
pre : " <b> 2.4.1. </b> "
---

Hệ thống được xây dựng theo kiến trúc **Serverless trên AWS** để tối ưu hóa chi phí vận hành và khả năng mở rộng.

## 1. Stack Công nghệ Chính

| Thành phần | Công nghệ lựa chọn | Lý do lựa chọn |
| :---: | :---: | :--- |
| **Ngôn ngữ** | Python 3.x | **Hệ sinh thái AI chuyên dụng**. Môi trường triển khai tối ưu cho LLM, Embedding và RAG. |
| **Compute** | AWS Lambda | **Kiến trúc Serverless hướng sự kiện**. Tối ưu chi phí vận hành (pay-per-request) và tích hợp liền mạch với hệ sinh thái AWS (S3, API Gateway). |
| **Raw Storage** | Amazon S3 | **Lưu trữ bền vững & Tối ưu chi phí**. Nơi chứa dữ liệu gốc an toàn, tích hợp sẵn sàng với các trigger xử lý tự động của Lambda. |
| **Vector DB** | Pinecone | **Managed Service**. Chuyên dụng cho Vector Search, tốc độ truy vấn cao, độ trễ thấp, không cần quản lý hạ tầng. |
| **Meta DB** | Amazon DynamoDB | **Độ trễ cực thấp cấp độ mili-giây**. Tối ưu cho các truy vấn khớp chính xác (Exact Match) và lưu trữ lịch sử hội thoại với tốc độ phản hồi tức thì. |
| **AI Model** | Amazon Bedrock | **Unified API**. Truy cập đa dạng Foundation Models qua một cổng kết nối duy nhất, đảm bảo tính riêng tư và an toàn dữ liệu tuyệt đối. |
| **CI/CD** | GitHub Actions | **Automation**. Tự động hóa quy trình Test và Deploy code lên Lambda ngay khi push code. |

## 2. Tài nguyên & Môi trường 

Trước khi đi vào triển khai chi tiết, cần đảm bảo các tài nguyên và quyền truy cập sau đã được thiết lập:

1.  **Tài khoản AWS & Region:**
    * Cần một tài khoản AWS đang hoạt động (Active Billing).

2.  **Tài khoản Dịch vụ bên thứ 3:**
    * **Pinecone:** Tạo tài khoản và lấy **API Key** (Serverless Index).
    * **GitHub:** Cấu hình **GitHub Secrets** để lưu trữ các credential (AWS_ACCESS_KEY, PINECONE_API_KEY) phục vụ cho CI/CD.

3.  **Môi trường Local:**
    * Cài đặt **AWS CLI v2** và cấu hình `aws configure`.
    * Cài đặt **Python 3.9+** và **SAM CLI** (Serverless Application Model) để build và deploy Lambda.