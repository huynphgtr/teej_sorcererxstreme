---
title : "Phần phát triển AI"
weight : 4
chapter : false
pre : " <b> 2.4. </b> "
---

## Giới thiệu

Tài liệu này mô tả chi tiết quy trình thiết lập môi trường, chuẩn hóa dữ liệu và xây dựng luồng xử lý AI (Embedding, RAG, Prompt Engineering, LLM) cho hệ thống tư vấn đa lĩnh vực: Tarot, Thần số học, Tử vi và Chiêm tinh học. Mục tiêu của hệ thống là cung cấp các câu trả lời logic, nhất quán, có kiểm soát dựa trên Knowledge Base chuẩn xác, giảm thiểu ảo giác (hallucination) của AI.

Báo cáo cũng sẽ đi sâu vào các thách thức kỹ thuật đã gặp phải (đặc biệt là xử lý dữ liệu tiếng Việt) và lý giải các quyết định lựa chọn công nghệ (Trade-off analysis).

---

## 1. Tổng quan kiến trúc & công nghệ

Hệ thống được xây dựng theo kiến trúc **Serverless trên AWS** để tối ưu hóa chi phí vận hành và khả năng mở rộng.

### Stack Công nghệ Chính

| Thành phần | Công nghệ lựa chọn | Lý do lựa chọn |
| :---: | :---: | :--- |
|Ngôn ngữ | Python 3.x | **Hệ sinh thái AI chuyên dụng**. Môi trường triển khai tối ưu cho LLM, Embedding và RAG. |
| Compute | AWS Lambda | **Kiến trúc Serverless hướng sự kiện**. Tối ưu chi phí vận hành (pay-per-request) và tích hợp liền mạch với hệ sinh thái AWS (S3, API Gateway). |
| Raw Storage | Amazon S3 | **Lưu trữ bền vững & Tối ưu chi phí**. Nơi chứa dữ liệu gốc an toàn, tích hợp sẵn sàng với các trigger xử lý tự động của Lambda. |
| Vector DB | Pinecone | **Managed Service**. Chuyên dụng cho Vector Search, tốc độ truy vấn cao, độ trễ thấp, không cần quản lý hạ tầng. |
| Meta DB | Amazon DynamoDB | **Độ trễ cực thấp cấp độ mili-giây**. Tối ưu cho các truy vấn khớp chính xác (Exact Match) và lưu trữ lịch sử hội thoại với tốc độ phản hồi tức thì. |
| AI Model | Amazon Bedrock | **Unified API**. Truy cập đa dạng Foundation Models qua một cổng kết nối duy nhất, đảm bảo tính riêng tư và an toàn dữ liệu tuyệt đối. |
| CI/CD | GitHub Actions | **Automation**. Tự động hóa quy trình Test và Deploy code lên Lambda ngay khi push code. |

---

## 2. Chiến lược dữ liệu

Đây là phần tốn nhiều công sức nghiên cứu nhất để đảm bảo chất lượng câu trả lời của AI (*Garbage In, Garbage Out*).

### Hành trình chuẩn hóa định dạng: Từ JSON sang JSONL

- Việc lựa chọn định dạng file lưu trữ đóng vai trò quyết định đến hiệu năng xử lý và chi phí bộ nhớ của AWS Lambda.

- Sau quá trình thử nghiệm, hệ thống quyết định chuẩn hóa toàn bộ dữ liệu Knowledge Base (Tarot, Tử vi, Thần số học) sang định dạng **JSONL (`.jsonl`)**. Đây là định dạng lưu trữ trong đó mỗi dòng của file là một đối tượng JSON hợp lệ riêng biệt, không phụ thuộc vào nhau.

#### Tại sao chọn JSONL?

* **Tối ưu bộ nhớ:** Sử dụng cơ chế **Lazy Loading** thay vì load toàn bộ file. Giúp mức tiêu thụ RAM của Lambda luôn thấp và ổn định, loại bỏ hoàn toàn lỗi tràn bộ nhớ (**OOM**) với dataset lớn.
* **Chịu lỗi cục bộ:** Lỗi cú pháp ở một dòng không làm "gãy" toàn bộ pipeline. Hệ thống tự động bỏ qua dòng lỗi và tiếp tục xử lý, đảm bảo tính liên tục của luồng dữ liệu.
* **Tương thích Streaming:** Hỗ trợ đọc luồng trực tiếp từ S3, giảm thiểu độ trễ khi bắt đầu xử lý Batch.

#### So sánh với giải pháp cũ (Standard JSON)

Định dạng JSON tiêu chuẩn ban đầu đã bộc lộ những điểm yếu chí mạng về hiệu năng:

| Đặc điểm | Giải pháp cũ: Standard JSON (`.json`) | Giải pháp hiện tại: JSONL (`.jsonl`) |
| :--- | :--- | :--- |
| Cấu trúc | - Mảng nguyên khối. <br> - Bao bọc bởi `[...]`, phân cách bằng dấu phẩy. | - Dòng độc lập. <br> - Mỗi dòng là một object riêng biệt. |
| Hiệu năng | - Memory Hog: Phải load cả file vào RAM để parse DOM. | - Memory Safe: Xử lý dòng nào, tốn RAM dòng đó. |
| Rủi ro | - Single Point of Failure: Sai 1 dấu phẩy = Hỏng toàn bộ file. | - Isolated: Lỗi dòng 1 không ảnh hưởng dòng 2. |

**Minh họa cấu trúc:**

* **JSON (Cũ - Mảng):**
    ```json
    [ {"id": 1, "text": "Aries..."}, {"id": 2, "text": "Taurus..."} ]
    ```
![JSON](/static/images/json.png)
*Hình 2.1: Mẫu dữ liệu dạng json.*

* **JSONL (Mới - Dòng):**
    ```json
    {"id": 1, "text": "Aries..."}
    {"id": 2, "text": "Taurus..."}
    ```
![JSONL](/static/images/jsonl.png)
*Hình 2.2: Cấu hình dữ liệu dạnh jsonl.*

### Kỹ thuật "Chia để trị" trong Embedding

* **Thách thức:** Dữ liệu thô (Raw text) quá dài và chứa nhiều keyword nhiễu. Khi RAG (Retrieval-Augmented Generation) truy vấn cả đoạn văn lớn, AI dễ bị "loãng" thông tin và trả lời lan man.
* **Giải pháp:** Thay vì embedding cả văn bản, hệ thống thực hiện:
    * *Flatten Contexts:* Chia nhỏ các trường thông tin trong contexts (ví dụ: `uu-diem`, `nhuoc-diem`, `tinh-yeu`).
    * *Meta-Injection:* Gắn kèm metadata (Tên, Category, Keyword) vào từng chunk nhỏ trước khi embedding.
* **Kết quả:** Khi search vector, hệ thống trích xuất đúng đoạn thông tin cần thiết (ví dụ: chỉ lấy đoạn "tình yêu của Bạch Dương" thay vì cả bài viết về Bạch Dương), giúp LLM trả lời chính xác trọng tâm.

---

## 3. Lưu trữ & Truy xuất

Hệ thống sử dụng cơ chế **Hybrid Retrieval** (kết hợp Vector Search và Key-Value Lookup). Trọng tâm của kiến trúc này là việc sử dụng Pinecone làm bộ não nhớ dài hạn (Long-term Memory) cho AI.

### Vector Database: Sức mạnh công nghệ của Pinecone

Thay vì tự vận hành hạ tầng, dự án lựa chọn **Pinecone** - một dịch vụ cơ sở dữ liệu vector chuyên dụng (Managed Vector Database). Đây là thành phần cốt lõi giúp AI "hiểu" được ngữ nghĩa của các câu hỏi về Tarot hay Tử vi, thay vì chỉ tìm kiếm từ khóa đơn thuần.

#### Cấu hình Index 
Dựa trên kiến trúc Serverless, hệ thống sử dụng chế độ **Serverless Index** của Pinecone để tối ưu chi phí (chỉ trả tiền theo lượng dữ liệu đọc/ghi, không mất phí duy trì server hoạt động).

![Cấu hình Pinecone Index](/static/images/pinecone1.jpg)
*Hình 3.1: Cấu hình Index thực tế trên Pinecone Console.*

**Thông số kỹ thuật quan trọng:**
* **Dimensions (Số chiều): 1024.** Đây là độ dài của vector. Độ dài này đủ lớn để mã hóa các sắc thái ngữ nghĩa phức tạp của các văn bản huyền học, nhưng vẫn tối ưu hơn so với các model 4096 chiều (quá nặng) hay 768 chiều (có thể thiếu chi tiết).
* **Metric (Thước đo): Cosine Similarity.** Hệ thống sử dụng Cosine để đo góc giữa hai vector. Trong không gian ngữ nghĩa, hai vector có góc càng nhỏ (Cosine tiệm cận 1) thì ý nghĩa càng tương đồng. Metric này phù hợp nhất cho các tác vụ NLP (Xử lý ngôn ngữ tự nhiên) so với Euclidean (khoảng cách).
* **Pod Type:** Serverless (Tự động scale theo nhu cầu, không cần provision trước).

#### Cấu trúc bản ghi
Sức mạnh thực sự của Pinecone nằm ở khả năng kết hợp **Vector Search** với **Metadata Filtering**.

![Cấu trúc Vector Record](/static/images/pinecone1.jpg)
*Hình 3.2: Chi tiết một bản ghi Vector bao gồm ID, Values và Metadata.*

Mỗi bản ghi (Record) được lưu trữ gồm 3 phần:
1.  **ID:** Định danh duy nhất (Hash từ nội dung gốc) để tránh trùng lặp dữ liệu (Dedup).
2.  **Values (Vector):** Mảng số thực gồm 1024 phần tử, đại diện cho ý nghĩa của đoạn văn bản.
3.  **Metadata (Siêu dữ liệu):** Đây là phần quan trọng nhất giúp tăng độ chính xác (Accuracy). Thay vì tìm trong "biển lớn", AI sẽ dùng metadata để khoanh vùng.
    * Ví dụ: Khi user hỏi về "Tình yêu của Sư Tử", hệ thống sẽ filter `category: "cung-hoang-dao"` và `entity_name: "Sư Tử"` trước, sau đó mới tìm vector gần nhất. Điều này loại bỏ hoàn toàn khả năng AI lấy nhầm thông tin của cung khác.

### Trade-off Analysis: Tại sao chọn Pinecone thay vì AWS RDS (pgvector)?

Trong hệ sinh thái AWS, giải pháp tiêu chuẩn thường là sử dụng **Amazon RDS for PostgreSQL** cài đặt extension `pgvector`. Tuy nhiên, sau khi cân nhắc kỹ lưỡng (Trade-off), Pinecone đã được lựa chọn.

Dưới đây là bảng phân tích so sánh chi tiết:

| Tiêu chí | Pinecone (Managed SaaS) | Amazon RDS + pgvector (Self-Managed) |
| :--- | :--- | :--- |
| **Kiến trúc** | **Native Vector DB.** Được thiết kế từ lõi chuyên dụng cho lưu trữ và tìm kiếm vector tốc độ cao. | **Relational DB + Extension.** Là database quan hệ truyền thống được "gắn thêm" khả năng xử lý vector. |
| **Vận hành (Ops)** | **Zero Ops.** Không cần quản lý server, không cần tinh chỉnh index thủ công. Chỉ cần gọi API. | **High Ops.** Phải quản lý instance, update version, tự cấu hình index (IVFFlat/HNSW), vacuum database định kỳ. |
| **Tốc độ (Latency)** | **Cực thấp (<50ms).** Tối ưu hóa cho truy vấn vector quy mô lớn. | Phụ thuộc vào cấu hình phần cứng (CPU/RAM) và cách tuning index. Dễ bị chậm khi dữ liệu lớn nếu không tối ưu tốt. |
| **Chi phí (Cost)** | **Pay-as-you-go.** Tính tiền dựa trên số lượng vector lưu trữ và số lần đọc/ghi. Rất rẻ khi khởi đầu dự án. | **Chi phí cố định (Hourly).** Phải trả tiền thuê instance chạy 24/7 ngay cả khi không có ai dùng (trừ khi dùng Aurora Serverless v2 nhưng giá khá cao). |
| **Khả năng lọc (Filtering)** | **Pre-filtering Native.** Hỗ trợ lọc metadata *trước* khi tìm kiếm vector (Single-stage filtering) rất mạnh mẽ. | Phải kết hợp câu lệnh SQL `WHERE` với vector search, đôi khi ảnh hưởng đến hiệu năng nếu index không chuẩn. |

**Kết luận:** Với quy mô dự án hiện tại và yêu cầu triển khai nhanh (Time-to-market), **Pinecone** là lựa chọn tối ưu nhờ tính chất Serverless, giúp đội ngũ tập trung vào phát triển tính năng AI thay vì tốn thời gian quản trị Database (DBA tasks).

### Tại sao lại cần thêm DynamoDB?
Mặc dù Pinecone rất mạnh về tìm kiếm ngữ nghĩa (Semantic Search), nhưng hệ thống vẫn cần DynamoDB vì:
* **Truy xuất định danh (Exact Match):** Lambda Metaphysical cần lấy thông tin chính xác tuyệt đối dựa trên ID (ví dụ: Ý nghĩa lá bài "The Fool" khi rút bài, hoặc thông tin sao "Tử Vi"). DynamoDB làm việc này nhanh và rẻ hơn vector search.
* **Tăng tốc độ (Latency):** Giảm tải cho Vector DB ở các tác vụ không cần AI suy luận.
* **Lưu trữ Dataset gốc:** Dùng làm nguồn backup và tra cứu nhanh metadata mà không cần decode vector.

---

## 4. AI Models & RAG Pipeline

Sử dụng **Amazon Bedrock** làm cổng kết nối tập trung.

### Embedding Model: Cohere Embed Multilingual
* **Lựa chọn:** `cohere.embed-multilingual-v3`.
* **Lý do:**
    * Hỗ trợ Tiếng Việt vượt trội so với các model Titan đời đầu.
    * Khả năng nắm bắt ngữ nghĩa (Semantic Nuance) trong các văn bản huyền học/tâm linh tốt hơn.
    * Kích thước vector (1024 dimensions) cân bằng giữa độ chính xác và chi phí lưu trữ.

### Large Language Model (LLM): Amazon Nova Pro
* **Lựa chọn:** `amazon.nova-pro-v1:0`.
* **Lý do:**
    * *Reasoning Capability:* Khả năng suy luận logic tốt, phù hợp để tổng hợp thông tin từ nhiều mảnh RAG rời rạc (ví dụ: ghép ý nghĩa 3 lá bài Tarot thành 1 câu chuyện).
    * *Context Window:* Đủ lớn để chứa prompt phức tạp và lịch sử chat.
    * *Cost/Performance:* Hiệu năng tốt với chi phí hợp lý hơn so với các dòng model thương mại cao cấp khác.

---

## 5. Tổng kết bài học & lộ trình phát triển

### Những thách thức kỹ thuật & Bài học xương máu
Quá trình xây dựng hệ thống AI tư vấn huyền học không chỉ là việc ghép nối các dịch vụ AWS lại với nhau, mà là cuộc chiến về **Chất lượng dữ liệu** và **Ngữ nghĩa**.

* **Vấn đề Nhiễu thông tin (RAG Hallucination):**
    * *Thực tế:* Dữ liệu huyền học thường trừu tượng. Khi User hỏi một câu mơ hồ (ví dụ: "Tương lai tôi thế nào?"), Vector Search dễ trả về các kết quả không liên quan (Noise), khiến LLM "ảo giác" bịa ra câu trả lời.
    * *Bài học:* Chất lượng của bộ dữ liệu (Knowledge Base) quan trọng hơn số lượng. Việc chia nhỏ (Chunking) dữ liệu theo cấu trúc `.jsonl` và gắn thẻ Metadata kỹ lưỡng là chìa khóa để tăng độ chính xác (Precision).
* **Thách thức Ngôn ngữ (Vietnamese Nuance):**
    * *Thực tế:* Các model Embedding quốc tế đôi khi không hiểu hết các thuật ngữ Hán-Việt trong Tử vi (ví dụ: "Cung Mệnh", "Thiên Di").
    * *Giải pháp:* Phải sử dụng model `cohere.embed-multilingual-v3` và kỹ thuật **Hybrid Search** (kết hợp tìm kiếm từ khóa chính xác của DynamoDB và tìm kiếm ngữ nghĩa của Pinecone) để bù đắp khiếm khuyết này.

### Hướng phát triển
Mục tiêu tiếp theo không chỉ là AI trả lời đúng, mà là trả lời **"đúng cho riêng BẠN"**. Hệ thống sẽ chuyển dịch từ tư vấn chung chung sang tư vấn định danh.

#### Cơ chế phản hồi người dùng (Feedback Loop - RLHF Lite)
Xây dựng cơ chế Like/Dislike cho từng câu trả lời. Dữ liệu này sẽ được lưu lại để:
1.  Tinh chỉnh lại Prompt (Prompt Tuning).
2.  Lọc bỏ các đoạn dữ liệu RAG kém chất lượng ra khỏi Pinecone.

---

### Lời kết
Hệ thống hiện tại đã thiết lập được một nền móng vững chắc: **Serverless** để tối ưu chi phí, **Pinecone** để xử lý trí nhớ, và **Python** để kết nối vạn vật.

Việc chuyển hướng sang **Cá nhân hóa** sẽ là bước nhảy vọt, biến hệ thống từ một công cụ "Tra cứu thông tin" (Search Engine) thành một "Trợ lý tâm linh" (Spiritual Companion) thực thụ, có khả năng thấu hiểu và đồng hành sâu sắc với người dùng.