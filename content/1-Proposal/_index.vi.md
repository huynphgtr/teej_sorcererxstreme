---
title: "Đề Án"
weight: 1
chapter: false
pre: " <b> 1. </b> "
---

# SorcererXStreme: Nền tảng Luận giải Ứng dụng AI

## 1. Tóm tắt  

Nền tảng SorcererXStreme AI là một nền tảng luận giải tâm linh dựa trên AI, được thiết kế để giúp người dùng khám phá bản thân thông qua nhiều lĩnh vực huyền học Đông và Tây khác nhau, bao gồm Chiêm tinh học (Astrology), Tarot, Thần số học (Numerology) và Tử vi Phương Đông (Eastern Horoscopes). Nền tảng của hệ thống là Lõi Tạo sinh Tăng cường Truy xuất (Retrieval-Augmented Generation - RAG Core), đảm bảo tất cả đầu ra đều dựa trên các nguồn tri thức huyền học được chọn lọc.

## 2. Vấn đề đặt ra

### Vấn đề

Người dùng hiện đang phải đối mặt với một số hạn chế khi khám phá kiến thức tâm linh và siêu hình:

*   **Thông tin rời rạc và chưa được xác minh:** Thông tin rải rác trên internet và thường thiếu độ tin cậy hoặc sự đối chiếu thích hợp.
*   **Khó khăn trong việc so sánh đa ngành:** Kết quả khó so sánh giữa các trường phái tư tưởng Phương Đông và Phương Tây.
*   **Thiếu cá nhân hóa và tương tác:** Hầu hết các ứng dụng cung cấp các bài đọc dạng tĩnh, thiếu chiều sâu của đối thoại cá nhân hóa và lời khuyên theo ngữ cảnh.
*   **Nội dung mơ hồ:** Nhiều ứng dụng mang tính giải trí thiếu chiều sâu, chưa được xác thực.

### Giải pháp

SorcererXStreme AI cung cấp một nền tảng thống nhất, trực quan và thông minh:

*   **Tương tác trực tiếp:** Người dùng trò chuyện trực tiếp với AI Chatbot, hỏi bất cứ điều gì về tính cách, vận mệnh hoặc các mối quan hệ của họ.
*   **Luận giải dựa trên mô hình tạo sinh tăng cường truy xuất RAG:** Hệ thống RAG đảm bảo rằng các luận giải dựa trên dữ liệu huyền học đã được xác minh, đảm bảo độ chính xác và chiều sâu.
*   **Trải nghiệm người dùng phân tầng:** Các tầng Miễn phí và VIP tối ưu hóa trải nghiệm người dùng và tạo ra một luồng doanh thu.
*   **Thiết kế tối ưu chi phí:** Một thiết kế hiện đại, nhẹ được triển khai nhanh chóng trên kiến trúc serverless AWS tối ưu hóa chi phí.

### Lợi ích và Lợi tức Đầu tư 

| Lợi ích              | Tác động                                                             | Giá trị                                        |
| :-------------------- | :------------------------------------------------------------------| :--------------------------------------------|
| **Độ tin cậy dữ liệu** | RAG làm giảm "ảo giác" của mô hình AI và cung cấp các luận giải có thể xác minh nguồn. | Độ tin cậy cao & giữ chân người dùng tốt hơn.  |
| **Tính trung tâm**     | Hợp nhất dữ liệu huyền học Đông và Tây trong một nền tảng.           | Cơ sở tri thức thống nhất cho người dùng.      |
| **Khả năng thu lại lợi nhuận** | Mô hình đăng ký VIP mở khóa các tính năng nâng cao. | Dòng doanh thu ổn định và khả năng kinh doanh. |
| **Chi phí vận hành**   | Kiến trúc serverless AWS được sử dụng.                               | Ước tính $80–$90/tháng cho MVP.                |

## 3. Kiến trúc giải pháp 

Nền tảng SorcererXStreme sử dụng kiến trúc hybrid serverless trên AWS, được thiết kế tỉ mỉ để xử lý các tương tác người dùng theo thời gian thực, các tác vụ theo lịch trình và giám sát tự động. Thiết kế toàn diện này đảm bảo tính toán chuyên biệt, khả năng mở rộng cao và bảo mật nghiêm ngặt trên tất cả các luồng chức năng.

![Sơ đồ kiến trúc](/images/High_Level_System_Architecture.drawio(2).png)


### Dịch vụ sử dụng

| Lớp | Dịch vụ | Vai trò và Mối quan hệ |
| :--- | :--- | :--- |
| **Edge & Auth** | Amplify, Cognito | - Amplify đảm nhận Hosting Frontend và Định tuyến.<br>- Cognito quản lý xác thực. |
| **API & Routing** | API Gateway | - Endpoint chính cho tất cả các Lambda Backend và Lambda AI. |
| **Compute Layer** | AWS Lambda | - Xử lý logic nghiệp vụ và các luồng Async/Sync. |
| **Data & Integration** | DynamoDB, Parameter Store, NeonDB, Pinecone | - NeonDB(PostgreSQL bên ngoài) là DB chính. <br> - DynamoDB cho lịch sử/truy cập nhanh. <br> - Parameter Store lưu trữ bảo mật. |
| **AI/ML** | Bedrock, Lambda (Embeddings), S3 |- Bedrock (LLM Model). <br> - S3 lưu trữ tài liệu thô RAG. <br> - Lambda Embeddings tạo vector. |
| **Async & Monitoring** | EventBridge, SQS, SES, CloudWatch, SNS | - Các luồng Async và Giám sát hoạt động độc lập. |
| **DevOps** | GitHub Actions, CloudFormation | - GitHub Actions đảm nhận quá trình Build, Test, và CloudFormation do Serverless Framework tạo ra là công cụ deploy chính. |


### Luồng Hoạt động

#### 1. Luồng Tương tác API Thời gian thực (Synchronous Flow)

Luồng này xử lý các yêu cầu chat và luận giải trực tiếp từ người dùng.

* **(1) Tiếp nhận Yêu cầu:** User gửi yêu cầu trực tiếp đến AWS Amplify Endpoint.
* **(2) Định tuyến & API:** Amplify chuyển tiếp yêu cầu đến API Gateway. API Gateway xác thực token Cognito và chuyển đến Lambda tương ứng (`SyncUser`, `Chatbot`, v.v.).
* **(3) Xử lý Dữ liệu:** Lambda truy cập Parameter Store để lấy các khóa bí mật và truy cập dữ liệu tại NeonDB, DynamoDB.
* **(4) RAG và AI:** Lambda Chatbot thực hiện luồng RAG:
    * Sử dụng Bedrock (Embedding Model) để tạo vector cho câu hỏi.
    * Truy vấn Pinecone (Vector Database) bằng vector đó.
    * Gửi ngữ cảnh RAG (Context) đến Bedrock (LLM) để tạo sinh câu trả lời.
* **(5) Phản hồi:** Lambda trả kết quả về API Gateway -> Amplify -> User.

#### 2. Luồng Thông báo Tự động (Asynchronous Flow)

Luồng này được đơn giản hóa mà không cần RDS.

* **(1) Kích hoạt:** EventBridge Scheduler kích hoạt Lambda TriggerReminder.
* **(2) Truy vấn Data:** Lambda truy vấn DynamoDB hoặc NeonDB để lấy danh sách người dùng đã đăng ký.
* **(3) Phân phối:** Lambda tạo nội dung và gửi email thông qua Amazon SES.

#### 3. Luồng triển khai (DevOps)

* **(1) Code Commit:** Developer đẩy code lên GitHub.
* **(2) Build & Deploy:** GitHub Actions kích hoạt quá trình Build, Test và sử dụng CloudFormation sinh ra từ Serverless Framework để triển khai các hàm Lambda, API Gateway và các tài nguyên khác lên AWS.


## 5. Mốc thời gian & Các cột mốc 

Dự án SorcererXStreme sẽ được thực hiện trong khoảng thời gian phát triển tập trung 9 tuần theo mô hình Agile-Iterative để nhanh chóng cung cấp một MVP với các tính năng chính.

### Mốc thời gian Dự án

| Iter  | Thời gian | Tuần | Trọng tâm chính | Các sản phẩm bàn giao chính |
| :-------------------------------------- | :-------| :-----------| :------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Iter 3: Thiết kế lại & Nguyên mẫu RAG** | 3 Tuần | 1 – 2 – 3 | Thiết kế Nền tảng & Tài liệu|- SRS v2 và SDS v2, Đề án được hoàn thiện.<br> - Sơ đồ kiến trúc AWS và bảng ước tính chi phí. <br> - Dữ liệu RAG được thu thập và pipeline ban đầu được thiết kế.             |
| **Iter 4: Vai trò & Hệ thống VIP**        | 3 Tuần    | 4 – 5 – 6     | Triển khai Logic Cốt lõi & Ủy quyền | - AWS Cognito được tích hợp để xác thực người dùng. <br> - Logic vai trò Guest/Miễn phí/VIP đầy đủ được triển khai và có thể kiểm thử. <br> - Corpus dữ liệu RAG được xây dựng trên S3.       |
| **Iter 5: Triển khai AWS & QA** | 3 Tuần    | 7 – 8 – 9     | Triển khai Đám mây & Ổn định | - Hệ thống chạy ổn định trên AWS. <br> - Hoàn thành kiểm thử đầu cuối đầy đủ. AWS Cost and Performance Sheet được hoàn thiện. <br> - Sẵn sàng cho môi trường thực thi. |

## 6. Ước tính ngân sách 
Dự án được chúng tôi giả định mức sử dụng thấp cho môi trường Demo (khoảng 5.000 yêu cầu/tháng).

### Chi phí Cơ sở Hạ tầng

| Layer |  Dịch vụ AWS | Mục tiêu | Chi phí |
| :---: | :--- | :--- | :---: |
| **I. COMPUTE & API** | | | |
| 1 | **AWS Lambda** | Xử lí Backend Logic (RAG, Compute) | $0.00 |
| 2 | **Amazon API Gateway** | Synchronous Request Gateway | $0.025 |
| 3 | **AWS Amplify** | Host Frontend (Next.js) | $2.59 |
| **II. DATA & STORAGE** | | | |
| 4 | **Amazon DynamoDB** | Chat History/Rate Limiting | $0.89 |
| 5 | **Amazon S3** | RAG Knowledge Base/Assets | $0.03 |
| **III. AI & SECURITY** | | | |
| 6 | **Amazon Bedrock** | Embedding & LLM/Content Generation | $2.65 |
| 7 | **Amazon Cognito** | Authentication/User Roles | $0.00 |
| 8 | **Parameter Store** | Store Master Keys | $0.00 |
| **IV. ASYNC & MONITORING** | | | |
| 9 | **EventBridge Scheduler** | Daily Horoscope Trigger | $0.00 |
| 10 | **Amazon SES** | Email Delivery | $0.48 |
| 11 | **Amazon CloudWatch** | Logs/Metrics/Alarms | $2.4 |
| 12 | **Amazon SNS** | Alert Notifications | $0.00 |
| 13 | **Cloudformation** | Deploy for dev | $0.00 |

Link: [Bảng uớc tính chi phí](https://drive.google.com/file/d/1Cy0ivN1wIOJ8DthIOE4cYYFG1BzESt5m/view?usp=sharing)

### Tổng Chi Phí Dự Án: $9.06/month


## 7. Đánh giá rủi ro 

| Rủi ro | Tác động   | Xác suất | Chiến lược giảm thiểu |
| :---------------------------------| :-------- | :-------- | :--------------------------------------------------------------------------------------------------------------------------------------------|
| **Ảo giác LLM** | Cao | Trung bình | Triển khai Bộ kiểm tra Thực tế RAG (RAG Fact Checker); sử dụng LLM chất lượng cao; căn cứ câu trả lời vào các nguồn đã được xác minh.      |
| **Vượt chi phí LLM** | Cao | Trung bình | Thiết lập Cảnh báo Ngân sách AWS (AWS Budget Alerts); triển khai kiểm soát token; sử dụng mô hình LLM phân tầng (Miễn phí so với VIP). |
| **Độ trễ Truy xuất RAG**            | Trung bình | Trung bình | Tối ưu hóa việc lập chỉ mục RAG (FAISS); tối ưu hóa kích thước chunk và lựa chọn mô hình embedding.                                            |
| **Vi phạm Bảo mật**                 | Cao        | Thấp       | Sử dụng Cognito để xác thực và Secret Manager để xử lý thông tin xác thực.                                                             |

## 8. Kết quả mong đợi

### Cải tiến kỹ thuật

*   **Độ chính xác theo thời gian thực:** Tích hợp RAG giảm đáng kể "ảo giác" của AI, nâng cao độ tin cậy của các luận giải.
*   **Khả năng mở rộng:** Kiến trúc serverless AWS đảm bảo khả năng mở rộng tự động để xử lý lưu lượng truy cập đáng kể của người dùng.

### Giá trị dài hạn

*   **Khả năng lợi nhuận:** Mô hình đăng ký VIP tạo ra một con đường rõ ràng, ổn định để tạo doanh thu.
*   **Nền tảng dữ liệu:** Một cơ sở tri thức huyền học độc quyền, đã được xác minh và được thiết lập như một tài sản có giá trị, có thể tái sử dụng.
*   **Mở rộng tương lai:** Kiến trúc AWS linh hoạt dễ dàng nâng cấp cho các ứng dụng di động hoặc tính năng trò chuyện bằng giọng nói.


