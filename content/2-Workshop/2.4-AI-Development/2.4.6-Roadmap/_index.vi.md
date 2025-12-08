---
title : "Bài học & Lộ trình"
weight : 6
chapter : false
pre : " <b> 2.4.6. </b> "
---


## 1. Những thách thức kỹ thuật & Bài học xương máu
Quá trình xây dựng hệ thống AI tư vấn huyền học không chỉ là việc ghép nối các dịch vụ AWS lại với nhau, mà là cuộc chiến về **Chất lượng dữ liệu** và **Ngữ nghĩa**.

* **Vấn đề Nhiễu thông tin (RAG Hallucination):**
    * *Thực tế:* Dữ liệu huyền học thường trừu tượng. Khi User hỏi một câu mơ hồ (ví dụ: "Tương lai tôi thế nào?"), Vector Search dễ trả về các kết quả không liên quan (Noise), khiến LLM "ảo giác" bịa ra câu trả lời.
    * *Bài học:* Chất lượng của bộ dữ liệu (Knowledge Base) quan trọng hơn số lượng. Việc chia nhỏ (Chunking) dữ liệu theo cấu trúc `.jsonl` và gắn thẻ Metadata kỹ lưỡng là chìa khóa để tăng độ chính xác (Precision).
* **Thách thức Ngôn ngữ (Vietnamese Nuance):**
    * *Thực tế:* Các model Embedding quốc tế đôi khi không hiểu hết các thuật ngữ Hán-Việt trong Tử vi (ví dụ: "Cung Mệnh", "Thiên Di").
    * *Giải pháp:* Phải sử dụng model `cohere.embed-multilingual-v3` và kỹ thuật **Hybrid Search** (kết hợp tìm kiếm từ khóa chính xác của DynamoDB và tìm kiếm ngữ nghĩa của Pinecone) để bù đắp khiếm khuyết này.

## 2. Hướng phát triển
Mục tiêu tiếp theo không chỉ là AI trả lời đúng, mà là trả lời **"đúng cho riêng BẠN"**. Hệ thống sẽ chuyển dịch từ tư vấn chung chung sang tư vấn định danh.

### Cơ chế phản hồi người dùng (Feedback Loop - RLHF Lite)
Xây dựng cơ chế Like/Dislike cho từng câu trả lời. Dữ liệu này sẽ được lưu lại để:
1.  Tinh chỉnh lại Prompt (Prompt Tuning).
2.  Lọc bỏ các đoạn dữ liệu RAG kém chất lượng ra khỏi Pinecone.

---

## 3. Lời kết
Hệ thống hiện tại đã thiết lập được một nền móng vững chắc: **Serverless** để tối ưu chi phí, **Pinecone** để xử lý trí nhớ, và **Python** để kết nối vạn vật.

Việc chuyển hướng sang **Cá nhân hóa** sẽ là bước nhảy vọt, biến hệ thống từ một công cụ "Tra cứu thông tin" (Search Engine) thành một "Trợ lý tâm linh" (Spiritual Companion) thực thụ, có khả năng thấu hiểu và đồng hành sâu sắc với người dùng.