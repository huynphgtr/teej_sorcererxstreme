---
title : "Lưu trữ & Truy xuất"
weight : 3
chapter : false
pre : " <b> 2.4.3. </b> "
---

Hệ thống sử dụng cơ chế **Hybrid Retrieval** (kết hợp Vector Search và Key-Value Lookup). Trọng tâm của kiến trúc này là việc sử dụng Pinecone làm bộ não nhớ dài hạn (Long-term Memory) cho AI.

## 1. Vector Database: Sức mạnh công nghệ của Pinecone

Thay vì tự vận hành hạ tầng, dự án lựa chọn **Pinecone** - một dịch vụ cơ sở dữ liệu vector chuyên dụng (Managed Vector Database). Đây là thành phần cốt lõi giúp AI "hiểu" được ngữ nghĩa của các câu hỏi về Tarot hay Tử vi, thay vì chỉ tìm kiếm từ khóa đơn thuần.

### Cấu hình Index 
Dựa trên kiến trúc Serverless, hệ thống sử dụng chế độ **Serverless Index** của Pinecone để tối ưu chi phí (chỉ trả tiền theo lượng dữ liệu đọc/ghi, không mất phí duy trì server hoạt động).

![Cấu hình Pinecone Index](/images/pinecone1.jpg)
*Hình 3.1: Cấu hình Index thực tế trên Pinecone Console.*

**Thông số kỹ thuật quan trọng:**
* **Dimensions (Số chiều): 1024.** Đây là độ dài của vector. Độ dài này đủ lớn để mã hóa các sắc thái ngữ nghĩa phức tạp của các văn bản huyền học, nhưng vẫn tối ưu hơn so với các model 4096 chiều (quá nặng) hay 768 chiều (có thể thiếu chi tiết).
* **Metric (Thước đo): Cosine Similarity.** Hệ thống sử dụng Cosine để đo góc giữa hai vector. Trong không gian ngữ nghĩa, hai vector có góc càng nhỏ (Cosine tiệm cận 1) thì ý nghĩa càng tương đồng. Metric này phù hợp nhất cho các tác vụ NLP (Xử lý ngôn ngữ tự nhiên) so với Euclidean (khoảng cách).
* **Pod Type:** Serverless (Tự động scale theo nhu cầu, không cần provision trước).

### Cấu trúc bản ghi
Sức mạnh thực sự của Pinecone nằm ở khả năng kết hợp **Vector Search** với **Metadata Filtering**.

![Cấu trúc Vector Record](/images/pinecone1.jpg)
*Hình 3.2: Chi tiết một bản ghi Vector bao gồm ID, Values và Metadata.*

Mỗi bản ghi (Record) được lưu trữ gồm 3 phần:
1.  **ID:** Định danh duy nhất (Hash từ nội dung gốc) để tránh trùng lặp dữ liệu (Dedup).
2.  **Values (Vector):** Mảng số thực gồm 1024 phần tử, đại diện cho ý nghĩa của đoạn văn bản.
3.  **Metadata (Siêu dữ liệu):** Đây là phần quan trọng nhất giúp tăng độ chính xác (Accuracy). Thay vì tìm trong "biển lớn", AI sẽ dùng metadata để khoanh vùng.
    * *Ví dụ:* Khi user hỏi về "Tình yêu của Sư Tử", hệ thống sẽ filter `category: "cung-hoang-dao"` và `entity_name: "Sư Tử"` trước, sau đó mới tìm vector gần nhất. Điều này loại bỏ hoàn toàn khả năng AI lấy nhầm thông tin của cung khác.

## 2. Trade-off Analysis: Tại sao chọn Pinecone thay vì AWS RDS (pgvector)?

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

## 3. Tại sao lại cần thêm DynamoDB?
Mặc dù Pinecone rất mạnh về tìm kiếm ngữ nghĩa (Semantic Search), nhưng hệ thống vẫn cần DynamoDB vì:
* **Truy xuất định danh (Exact Match):** Lambda Metaphysical cần lấy thông tin chính xác tuyệt đối dựa trên ID (ví dụ: Ý nghĩa lá bài "The Fool" khi rút bài, hoặc thông tin sao "Tử Vi"). DynamoDB làm việc này nhanh và rẻ hơn vector search.
* **Tăng tốc độ (Latency):** Giảm tải cho Vector DB ở các tác vụ không cần AI suy luận.
* **Lưu trữ Dataset gốc:** Dùng làm nguồn backup và tra cứu nhanh metadata mà không cần decode vector.