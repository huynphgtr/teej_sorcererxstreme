---
title : "CI/CD & Testing"
weight : 5
chapter : false
pre : " <b> 2.4.5. </b> "
---

Hệ thống áp dụng quy trình CI/CD tự động hóa hoàn toàn thông qua **GitHub Actions**, đảm bảo code luôn được kiểm tra (Test) trước khi triển khai (Deploy) lên môi trường AWS Lambda.

## 1. Cơ chế CI – Continuous Integration

Mỗi khi có code mới được đẩy lên (Push) hoặc tạo Pull Request vào nhánh `main`, quy trình CI sẽ tự động kích hoạt để đảm bảo tính toàn vẹn của logic.

**Luồng xử lý:**
`[Code Commit] ➔ [GitHub Actions Trigger] ➔ [Runner Ubuntu Start] ➔ [Setup Python Env] ➔ [Run Pytest] ➔ [Result: ✅ Pass / ❌ Fail]`

* **Mocking Strategy:** Toàn bộ Unit Test sử dụng thư viện `unittest.mock` để giả lập các dịch vụ AWS (S3, DynamoDB, Bedrock). Điều này giúp:
    * **Zero Cost:** Chạy test không tốn phí gọi API AWS.
    * **High Speed:** Test chạy trong vài giây thay vì chờ phản hồi mạng.
    * **Safety:** Không bao giờ ghi đè hay xóa dữ liệu thật trên Production.

## 2. Cơ chế CD – Continuous Deployment

Chỉ khi quy trình CI trả về kết quả **Success (✅)**, luồng CD mới được kích hoạt để đóng gói và đẩy code lên AWS.

**Luồng xử lý:**
`[CI Success] ➔ [Install Binary Libs] ➔ [Package Zip 📦] ➔ [AWS CLI Upload 🚀] ➔ [Lambda Live Update]`

**Lưu ý về Gói Deploy (Deployment Package):**
Một thách thức lớn với AWS Lambda là sự tương thích của các thư viện C-extension (như `numpy` trong xử lý tính toán). Hệ thống giải quyết bằng cách cài đặt và build thư viện với flag `--platform manylinux2014_x86_64` ngay trong quy trình CD để đảm bảo tương thích tuyệt đối với môi trường Amazon Linux của Lambda.

## 3. Chiến lược Unit Testing

Hệ thống kiểm thử bao phủ 3 lớp logic: **Input Validation**, **Happy Path** (Luồng chạy đúng), và **Error Handling** (Xử lý lỗi).

### Chatbot Lambda (`sorcererxstreme-chatbot`)
* **Input Validation:**
    * *Case 1 (Missing Session):* Gửi thiếu `sessionId` → Trả về **400 Bad Request**.
    * *Case 2 (Missing Question):* Gửi thiếu nội dung câu hỏi → Trả về **400**.
    * *Case 3 (Invalid JSON):* Body không đúng định dạng JSON → Trả về lỗi **Parse Error**.
* **Happy Path:**
    * *Case 4:* Giả lập luồng RAG đầy đủ: `Load History` → `Query Pinecone` → `Call Bedrock` → `Save Chat`. Đảm bảo AI trả về phản hồi **200 OK**.

### Embedding Lambda (`sorcererxstreme-embedding`)
* **Logic & Utilities:**
    * *Case 1 (Flatten Contexts):* Kiểm tra hàm làm phẳng JSON. Input `{"hobbies": ["code", "read"]}` phải thành chuỗi `"hobbies: code, read"`.
    * *Case 2 (Bedrock Fail):* Giả lập Bedrock bị lỗi mạng. Hàm `get_embedding` phải trả về `None` để không làm sập luồng Batch.
* **Integration Flow:**
    * *Case 3 (Happy Flow):* Giả lập đọc file JSONL từ S3 → Embed 2 items → Ghi vào DynamoDB & Pinecone. Kiểm tra số lần gọi API phải khớp với số items.
    * *Case 4 (S3 Error):* Giả lập không tìm thấy file S3 → Hệ thống báo lỗi **500 (Internal Error)**.

### Metaphysical Lambda (`sorcererxstreme-metaphysical`)
Đây là function phức tạp nhất với logic rẽ nhánh theo Domain.

* **Input Validation:**
    * *Case 1 (Invalid Domain):* Gửi `domain: "bitcoin"` → Trả về **400** (Chỉ hỗ trợ Tarot, Horoscope, Numerology, Astrology).
    * *Case 2 (Missing Context):* Chọn "Tử Vi" nhưng thiếu `birth_date` → Trả về yêu cầu nhập liệu.
    * *Case 3 (Empty Tarot):* Chọn "Tarot" nhưng mảng `cards_drawn` rỗng → Cảnh báo chọn bài.
* **Domain Logic:**
    * *Case 4 (Tarot Flow):* Mock DynamoDB trả về ý nghĩa lá bài "The Sun". Kiểm tra AI có nhận được đúng context để phân tích không.
    * *Case 5 (Horoscope Flow):* Kiểm tra tích hợp thư viện `lasotuvi`. Mock object "Thiên Bàn" để đảm bảo AI nhận được thông tin "Mệnh: Lộ Bàng Thổ".
    * *Case 6 (Graceful Degradation):* Khi Bedrock quá tải (Throttling), hệ thống trả về thông báo thân thiện thay vì crash.