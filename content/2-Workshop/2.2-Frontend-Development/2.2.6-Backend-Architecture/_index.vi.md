---
title: "Kiến trúc Backend tham khảo (RAG & Database)"
weight: 6
chapter: false
pre: " <b> 2.2.6. </b> "
---


Là một Frontend Developer hiện đại, ranh giới giữa Frontend và Backend ngày càng mờ nhạt. Bạn không cần phải viết code SQL hay quản lý server, nhưng bạn bắt buộc phải **hiểu** luồng dữ liệu để tích hợp hiệu quả.

Hệ thống SorcererXtreme sử dụng kiến trúc **RAG (Retrieval-Augmented Generation)** tiên tiến. Hãy cùng mổ xẻ xem điều gì xảy ra sau khi bạn gọi `fetch('/api/chat')`.

### 1. RAG Flow: Tại sao AI trả lời chính xác?

Nếu chỉ đơn thuần gửi câu hỏi cho ChatGPT, nó sẽ "chém gió" dựa trên dữ liệu cũ. Để AI đóng vai một "Phù thủy thông thái" am hiểu Tarot, chúng ta dùng quy trình 4 bước:

1.  **Retrieval (Truy tìm):**
    *   Khi User hỏi *"Lá bài The Fool có ý nghĩa gì?"*, câu hỏi được chuyển thành vector (dạng số).
    *   Hệ thống tìm trong **Pinecone** (Vector DB) những đoạn văn bản trong sách Tarot có ý nghĩa tương đồng nhất.
2.  **Augmentation (Tăng cường):**
    *   Hệ thống ghép câu hỏi gốc + nội dung tìm được từ Pinecone thành một Prompt hoàn chỉnh.
    *   *Prompt: "Dựa vào kiến thức sau [trích đoạn sách...], hãy trả lời câu hỏi: Lá bài The Fool có ý nghĩa gì?"*
3.  **Generation (Tạo sinh):**
    *   Gửi Prompt này đến **Amazon Bedrock** (chứa model Claude 3 hoặc Titan).
    *   AI sẽ trả lời dựa trên chính xác những gì sách viết, tránh bịa đặt.
4.  **Response:** Frontend nhận câu trả lời cuối cùng và hiển thị.

### 2. Chiến lược "Đa cơ sở dữ liệu" (Polyglot Persistence)

Một ứng dụng lớn không bao giờ chỉ dùng một loại Database. Chúng ta dùng đúng công cụ cho đúng việc:

**A. NeonDB (Serverless PostgreSQL)**
*   **Loại:** Quan hệ (Relational).
*   **Dữ liệu:** User Profile, Thông tin gói VIP, Giao dịch thanh toán.
*   **Tại sao:** Dữ liệu tiền bạc cần tính toàn vẹn (ACID) cao nhất. SQL là lựa chọn số 1.

**B. Amazon DynamoDB**
*   **Loại:** NoSQL (Key-Value).
*   **Dữ liệu:** Lịch sử Chat, Logs hoạt động.
*   **Tại sao:** Chat sinh ra hàng triệu bản ghi. DynamoDB có thể ghi/đọc cực nhanh với độ trễ thấp (single-digit millisecond) bất kể data lớn cỡ nào.

**C. Pinecone**
*   **Loại:** Vector Database.
*   **Dữ liệu:** Kiến thức Tarot, Chiêm tinh (đã được mã hóa thành Vector).
*   **Tại sao:** SQL hay NoSQL không thể tìm kiếm theo "ngữ nghĩa" (semantic search). Chỉ Vector DB mới hiểu rằng "Vua tiền" và "King of Pentacles" là liên quan nhau.

### 3. Bảo mật: Mô hình "Pháo đài" (Fortress)

Frontend của bạn (`sorcererxtreme.vn`) là vùng đất công cộng ai cũng vào được. Nhưng Backend là "thánh địa".

*   **Không lộ diện Database:** NeonDB hay Pinecone không bao giờ mở cổng ra Internet.
*   **API Gateway là lính gác:** Mọi yêu cầu phải đi qua API Gateway. Nó kiểm tra "thẻ bài" (Cognito Token). Nếu không có hoặc hết hạn -> Chặn ngay lập tức (401 Unauthorized).
*   **Lambda là người vận chuyển:** Chỉ có Lambda function (nằm trong vùng mạng riêng VPC) mới có chìa khóa (Secret Key) để mở cửa vào Database.

---
{{% notice info %}}
**Takeaway:** Khi debug lỗi "tại sao AI trả lời sai?", Frontend Dev có thể đoán ngay: "À, có thể bước **Retrieval** ở Pinecone tìm sai context", thay vì đổ lỗi ngay cho Model AI. Hiểu hệ thống giúp bạn fix bug nhanh hơn!
{{% /notice %}}
