---
title : "Chiến lược dữ liệu"
weight : 2
chapter : false
pre : " <b> 2.4.2. </b> "
---


Đây là phần tốn nhiều công sức nghiên cứu nhất để đảm bảo chất lượng câu trả lời của AI (*Garbage In, Garbage Out*).

## 1. Hành trình chuẩn hóa định dạng: Từ JSON sang JSONL

- Việc lựa chọn định dạng file lưu trữ đóng vai trò quyết định đến hiệu năng xử lý và chi phí bộ nhớ của AWS Lambda.
- Sau quá trình thử nghiệm, hệ thống quyết định chuẩn hóa toàn bộ dữ liệu Knowledge Base (Tarot, Tử vi, Thần số học) sang định dạng **JSONL (`.jsonl`)**. Đây là định dạng lưu trữ trong đó mỗi dòng của file là một đối tượng JSON hợp lệ riêng biệt, không phụ thuộc vào nhau.

### Tại sao chọn JSONL?

* **Tối ưu bộ nhớ:** Sử dụng cơ chế **Lazy Loading** thay vì load toàn bộ file. Giúp mức tiêu thụ RAM của Lambda luôn thấp và ổn định, loại bỏ hoàn toàn lỗi tràn bộ nhớ (**OOM**) với dataset lớn.
* **Chịu lỗi cục bộ:** Lỗi cú pháp ở một dòng không làm "gãy" toàn bộ pipeline. Hệ thống tự động bỏ qua dòng lỗi và tiếp tục xử lý, đảm bảo tính liên tục của luồng dữ liệu.
* **Tương thích Streaming:** Hỗ trợ đọc luồng trực tiếp từ S3, giảm thiểu độ trễ khi bắt đầu xử lý Batch.

### So sánh với giải pháp cũ (Standard JSON)

Định dạng JSON tiêu chuẩn ban đầu đã bộc lộ những điểm yếu chí mạng về hiệu năng:

| Đặc điểm | Giải pháp cũ: Standard JSON (`.json`) | Giải pháp hiện tại: JSONL (`.jsonl`) |
| :--- | :--- | :--- |
| **Cấu trúc** | - Mảng nguyên khối. <br> - Bao bọc bởi `[...]`, phân cách bằng dấu phẩy. | - Dòng độc lập. <br> - Mỗi dòng là một object riêng biệt. |
| **Hiệu năng** | - Memory Hog: Phải load cả file vào RAM để parse DOM. | - Memory Safe: Xử lý dòng nào, tốn RAM dòng đó. |
| **Rủi ro** | - Single Point of Failure: Sai 1 dấu phẩy = Hỏng toàn bộ file. | - Isolated: Lỗi dòng 1 không ảnh hưởng dòng 2. |

**Minh họa cấu trúc:**

* **JSON (Cũ - Mảng):**
    ```json
    [ {"id": 1, "text": "Aries..."}, {"id": 2, "text": "Taurus..."} ]
    ```
![JSON](/images/json.png)
*Hình 2.1: Mẫu dữ liệu dạng json.*

* **JSONL (Mới - Dòng):**
    ```json
    {"id": 1, "text": "Aries..."}
    {"id": 2, "text": "Taurus..."}
    ```
![JSONL](/images/jsonl.png)
*Hình 2.2: Cấu hình dữ liệu dạnh jsonl.*

## 2. Kỹ thuật "Chia để trị" trong Embedding

* **Thách thức:** Dữ liệu thô (Raw text) quá dài và chứa nhiều keyword nhiễu. Khi RAG (Retrieval-Augmented Generation) truy vấn cả đoạn văn lớn, AI dễ bị "loãng" thông tin và trả lời lan man.
* **Giải pháp:** Thay vì embedding cả văn bản, hệ thống thực hiện:
    * *Flatten Contexts:* Chia nhỏ các trường thông tin trong contexts (ví dụ: `uu-diem`, `nhuoc-diem`, `tinh-yeu`).
    * *Meta-Injection:* Gắn kèm metadata (Tên, Category, Keyword) vào từng chunk nhỏ trước khi embedding.
* **Kết quả:** Khi search vector, hệ thống trích xuất đúng đoạn thông tin cần thiết (ví dụ: chỉ lấy đoạn "tình yêu của Bạch Dương" thay vì cả bài viết về Bạch Dương), giúp LLM trả lời chính xác trọng tâm.

## 3. Thiết kế Cơ sở dữ liệu (DynamoDB)

Để đảm bảo tốc độ truy xuất dưới 10ms cho các ứng dụng Real-time, hệ thống sử dụng **Amazon DynamoDB** với thiết kế 2 bảng riêng biệt, phục vụ hai mục đích cốt lõi: Lưu trữ tri thức (Knowledge Base) và Lưu trữ ngữ cảnh hội thoại (Chat History).

![DynamoDB Tables](/images/dynamodb1.png)
*Hình 3.1: Danh sách các bảng DynamoDB đang hoạt động.*

### Bảng Tri thức (MetaphysicalKnowledgeBase)
Đây là "kho tàng" dữ liệu gốc của hệ thống, nơi lưu trữ thông tin chi tiết về Tarot, Các cung hoàng đạo, và Thần số học. Dữ liệu tại đây được dùng làm nguồn tham chiếu chính xác (Ground Truth) cho quá trình RAG.

* **Partition Key (PK):** `category` (String). Ví dụ: `Tarot_card`, `Zodiac_sign`. Giúp phân nhóm dữ liệu lớn để truy vấn theo loại hình.
* **Sort Key (SK):** `entity_name` (String). Ví dụ: `Ace of Cups`, `Aries`. Định danh duy nhất cho từng thực thể trong nhóm.

**Cấu trúc Item chi tiết:**
Hệ thống không lưu trữ văn bản phẳng mà lưu dưới dạng cấu trúc JSON trong thuộc tính `contexts`. Điều này cho phép trích xuất linh hoạt từng khía cạnh (Tình yêu, Sự nghiệp, Sức khỏe) thay vì lấy toàn bộ bài viết.

![Knowledge Base Item](/images/dynamodb2.png)
*Hình 3.2: Chi tiết thuộc tính của một lá bài Tarot (Ace of Cups).*

| Attribute Name | Type | Mô tả |
| :--- | :--- | :--- |
| `contexts` | String (JSON) | Chứa các đoạn nội dung đã phân loại (`general_upright`, `love_reversed`, v.v.). Đây là nguồn dữ liệu chính để Embedding. |
| `keywords` | List | Danh sách từ khóa liên quan, hỗ trợ bổ trợ cho Semantic Search. |

### Bảng Lịch sử Chat (sorcererxtreme-chatMessages)
Để AI có thể "nhớ" được những gì người dùng vừa hỏi (Context Retention), hệ thống cần một bộ nhớ ngắn hạn. Bảng này lưu trữ toàn bộ lịch sử hội thoại theo phiên làm việc.

* **Partition Key (PK):** `sessionId` (String). Mã định danh phiên chat của người dùng.
* **Sort Key (SK):** `timestamp` (String). Thời gian gửi tin nhắn (ISO 8601), giúp sắp xếp hội thoại theo đúng trình tự thời gian.

![Chat History Item](/images/dynamodb3.png)
*Hình 3.3: Mẫu dữ liệu lưu trữ một tin nhắn của người dùng.*

**Luồng xử lý:**
Mỗi khi người dùng gửi tin nhắn mới:
1.  Hệ thống truy vấn bảng này với `sessionId` hiện tại.
2.  Lấy ra $N$ tin nhắn gần nhất (Context Window).
3.  Ghép lịch sử này vào Prompt gửi cho LLM để đảm bảo câu trả lời mạch lạc, không bị đứt đoạn ngữ cảnh.