---
title : "Phần phát triển AI"
weight : 4
chapter : false
pre : " <b> 2.4. </b> "
---

## 1. Giới thiệu

Tài liệu này là một Technical Case Study mô tả quy trình xây dựng hệ thống AI tư vấn đa lĩnh vực (Tarot, Thần số học, Tử vi, Chiêm tinh) dựa trên kiến trúc Serverless. Mục tiêu cốt lõi là giải quyết bài toán "ảo giác" của AI thông qua luồng RAG (Retrieval-Augmented Generation) chặt chẽ, đảm bảo câu trả lời luôn bám sát Knowledge Base đã được kiểm chứng.

### Đặt vấn đề: Thách thức của AI trong lĩnh vực Huyền học

Xây dựng AI cho lĩnh vực huyền học đặt ra những rào cản kỹ thuật đặc thù mà các giải pháp chatbot thông thường khó đáp ứng:

* **Tính trừu tượng và Đa nghĩa:** Dữ liệu chứa nhiều thuật ngữ Hán-Việt và khái niệm trừu tượng (ví dụ: ý nghĩa lá bài thay đổi theo vị trí trải, sao chiếu mệnh thay đổi theo giờ sinh). Các LLM phổ quát thường gặp khó khăn trong việc nắm bắt ngữ cảnh hẹp này.
* **Yêu cầu độ chính xác tuyệt đối:** Người dùng tìm kiếm lời khuyên cần sự chính xác. Việc AI tự "bịa" kiến thức (như sai vị trí sao, sai ý nghĩa số) là rủi ro nghiêm trọng nhất cần loại bỏ.
* **Xử lý dữ liệu phi cấu trúc:** Knowledge Base đầu vào không đồng nhất, đòi hỏi quy trình chuẩn hóa (ETL) phức tạp để chuyển đổi thành Vector Embedding mà không làm mất đi ngữ nghĩa gốc.

### Chiến lược Tối ưu Chi phí

Một trong những ưu tiên hàng đầu của dự án là tối ưu hóa chi phí vận hành ngay từ ngày đầu tiên. Thay vì duy trì các cụm server tốn kém 24/7, hệ thống áp dụng triệt để mô hình **Pay-as-you-go**.

Kiến trúc này giúp giảm thiểu rủi ro tài chính cho giai đoạn MVP (Minimum Viable Product), khi lượng người dùng chưa ổn định. Dưới đây là bảng phân tích chi phí đơn vị cho các dịch vụ chính:

| Dịch vụ | Vai trò | Mô hình tính phí | Đơn giá ước tính |
| :--- | :--- | :--- | :--- |
| AWS Lambda | Xử lý logic Backend | Theo số lần request & thời gian chạy | ~$0.20 / 1 triệu requests |
| Pinecone Serverless | Vector Database (Lưu trữ) | Theo dung lượng lưu trữ & Read Units | ~$0.33 / GB / tháng |
| Amazon Bedrock | LLM Inference (Nova Pro) | Theo Input/Output Tokens | Input: ~$0.0008 / 1k tokens<br>Output: ~$0.0032 / 1k tokens |
| Cohere Embed | Embedding Model (Multilingual) | Theo số tokens đã xử lý | ~$0.10 / 1 triệu tokens |
| DynamoDB | Metadata & Cache | Write/Read Capacity Units (On-demand) | ~$1.25 / 1 triệu Write Units |

> **Nhận định:** Với kiến trúc này, chi phí duy trì hệ thống khi không có người dùng (Idle state) gần như bằng 0.

## 2. Sơ đồ kiến trúc mảng AI

![AI-Architecture](/images/AI-architecture.png)

## Nội dung chi tiết

1.  [Kiến trúc hệ thống](2.4.1-Architecture/)
2.  [Chiến lược dữ liệu](2.4.2-Data-strategy/)
3.  [Lưu trữ & Truy xuất](2.4.3-Storage-retrieval/)
4.  [Mô hình AI & RAG](2.4.4-Models-rag/)
5.  [CI/CD & Testing](2.4.5-CICD/)
6.  [Bài học & Roadmap](2.4.6-Roadmap/)