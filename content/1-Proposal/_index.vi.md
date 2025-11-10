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
| :--------------------: | :------------------------------------------------------------------: | :--------------------------------------------: |
| **Độ tin cậy dữ liệu** | RAG làm giảm "ảo giác" của mô hình AI và cung cấp các luận giải có thể xác minh nguồn. | Độ tin cậy cao & giữ chân người dùng tốt hơn.  |
| **Tính trung tâm**     | Hợp nhất dữ liệu huyền học Đông và Tây trong một nền tảng.           | Cơ sở tri thức thống nhất cho người dùng.      |
| **Khả năng thu lại lợi nhuận** | Mô hình đăng ký VIP mở khóa các tính năng nâng cao. | Dòng doanh thu ổn định và khả năng kinh doanh. |
| **Chi phí vận hành**   | Kiến trúc serverless AWS được sử dụng.                               | Ước tính $80–$90/tháng cho MVP.                |

## 3. Kiến trúc giải pháp 

Nền tảng SorcererXStreme sử dụng kiến trúc serverless lai (hybrid serverless) trên AWS, được thiết kế tỉ mỉ để xử lý các tương tác người dùng theo thời gian thực, các tác vụ theo lịch trình và giám sát tự động. Thiết kế toàn diện này đảm bảo tính toán chuyên biệt, khả năng mở rộng cao và bảo mật nghiêm ngặt trên tất cả các luồng chức năng.

![Sơ đồ kiến trúc](/images/architecture.jpg)


### Các dịch vụ AWS được sử dụng

| Lớp                       | Dịch vụ AWS                         | Vai trò chính trong SorcererXStreme AI                                                                                                                                                                                              |
| :----------------------- | :---------------------------------| :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Mạng & Biên**           | Route 53, CloudFront, WAF           | - Route 53 xử lý định tuyến DNS.<br> - CloudFront (CDN) tăng tốc phân phối nội dung. <br> - WAF cung cấp tính năng lọc lưu lượng và bảo mật chống lại các khai thác web.                                                                           |
| **Bảo mật & Danh tính**   | Cognito, Secrets Manager | - Cognito quản lý xác thực và ủy quyền người dùng.<br> - Secrets Manager lưu trữ và quản lý an toàn các thông tin xác thực nhạy cảm.                                                                                                        |
| **Tính toán (API)**       | App Runner, API Gateway, AWS Lambda | - App Runner host frontend Next.js hoặc các containers backend cốt lõi. <br> - API Gateway hoạt động như điểm vào đồng bộ, định tuyến các yêu cầu đến các Lambda Functions chuyên dụng (Chatbot, Metaphysical API, History API).             |
| **Bất đồng bộ & Sự kiện** | EventBridge Scheduler, SQS, SES | - EventBridge Scheduler kích hoạt chức năng tử vi hàng ngày. <br> - SQS đệm và tách rời việc xử lý tin nhắn (fan-out/fan-in) cho các lời nhắc. <br> - SES gửi email thông báo cho người dùng. |
| **Tầng AI/ML** | Amazon Bedrock | Cung cấp quyền truy cập được quản lý vào các Mô hình Nền tảng (LLMs) cho AI Chatbot, xử lý ngữ cảnh RAG và tạo nội dung.                                                                                                    |
| **Dữ liệu & Lưu trữ**     | RDS for PostgreSQL, DynamoDB, S3    | - RDS lưu trữ dữ liệu quan hệ (hồ sơ người dùng chi tiết, danh sách người dùng cho lời nhắc).<br> - DynamoDB lưu trữ dữ liệu truy cập nhanh, tần suất cao (Lịch sử Chat, Lịch sử Luận giải). <br> - S3 lưu trữ tài sản tĩnh và cơ sở tri thức RAG. |
| **Giám sát & DevOps**     | CloudWatch, SNS, CodePipeline | - CloudWatch thu thập nhật ký (logs) và số liệu (metrics). <br> - SNS gửi cảnh báo quan trọng đến các nhà phát triển. <br> - CodePipeline/CodeBuild quản lý quy trình CI/CD từ GitHub đến triển khai.                                               |

### Thiết kế thành phần 

Hoạt động của nền tảng SorcererXStreme được xác định bởi bốn luồng chức năng riêng biệt, liên kết với nhau, bao quát tất cả các hoạt động được thể hiện trong sơ đồ Kiến trúc Hệ thống cấp cao:

#### 1. Tương tác API thời gian thực (Luồng đồng bộ)

* **Tiếp nhận yêu cầu (1):** Yêu cầu của người dùng được nhận và lọc tại tầng biên (Edge Layer) thông qua **Route 53** → **CloudFront** (để caching) → **WAF** (để bảo mật).
* **Định tuyến & Logic (2, 4):** Yêu cầu được chuyển tiếp đến **API Gateway** hoặc **App Runner**. API Gateway định tuyến lưu lượng đến các hàm **Lambda** cụ thể. 
* **Hoạt động Chatbot:** Lambda Chatbot đọc ngữ cảnh từ **DynamoDB** (Đọc chat), xử lý yêu cầu, và gọi **Bedrock** để tạo sinh nội dung LLM.
* **Bảo mật dữ liệu (3):** Bất kỳ Lambda nào yêu cầu truy cập database hoặc khóa LLM đều phải lấy thông tin xác thực một cách an toàn từ **Secrets Manager**.
* **Lưu trữ dữ liệu (5):** Kết quả cuối cùng hoặc các luận giải được lưu (Save History) vào **DynamoDB** hoặc **RDS for PostgreSQL**.

#### 2. Thông báo Tử vi Hàng ngày (Luồng Bất đồng bộ)

* **Kích hoạt (6):** Luồng bắt đầu thông qua **EventBridge Scheduler**, được kích hoạt vào một thời điểm đã định trước để gọi hàm `TriggerReminderLambda`.
* **Phân tán (Fan-Out) (7):** Hàm Lambda này truy vấn **RDS** để lấy danh sách người dùng đã đăng ký nhận thông báo và đẩy các tin nhắn riêng lẻ vào **Hàng đợi SQS (Reminder Queue)**.
* **Phân phối (8):** Hàm `SendReminderLambda` riêng biệt được kích hoạt bởi SQS, tạo nội dung email cuối cùng và gửi thông báo đến người dùng thông qua **Amazon SES**.

#### 3. Luồng Giám sát & Cảnh báo

* **Ghi nhật ký (9):** Tất cả các dịch vụ đang hoạt động (**Lambda, RDS, DynamoDB, App Runner**) liên tục công bố logs và metrics của chúng lên **Amazon CloudWatch**.
* **Cảnh báo:** **CloudWatch Alarm** chủ động giám sát các chỉ số hoạt động quan trọng và sử dụng **Amazon SNS** để gửi cảnh báo khẩn cấp đến đội ngũ phát triển và vận hành.

#### 4. DevOps (Luồng CI/CD)

* **Cập nhật Mã (10):** Nhà phát triển đẩy mã mới lên **GitHub**, điều này kích hoạt quy trình CI/CD được quản lý bởi **AWS CodePipeline/CodeBuild**.
* **Triển khai:** Pipeline tự động đóng gói ứng dụng và triển khai phiên bản mới đến Tầng Tính toán (**Compute Layer**), đảm bảo cập nhật nhất quán và tự động.

## 4. Triển khai kỹ thuật 

Dự án SorcererXStreme xây dựng dựa trên phương pháp **Phát triển Agile-Iterative**, tập trung vào việc cung cấp một phần gia tăng hoạt động với vai trò người dùng mở rộng và tích hợp RAG trong mỗi chu kỳ. Việc phát triển được cơ cấu thành một **lộ trình 9 tuần** bao gồm ba giai đoạn chính (Iter 3, 4 và 5) sau giai đoạn lập kế hoạch ban đầu.

### Các giai đoạn triển khai

Quá trình phát triển bao gồm bốn giai đoạn chính, được chia thành ba giai đoạn tập trung kéo dài 3 tuần:

| Giai đoạn                                   | Thời gian      | Lĩnh vực tập trung |
| :----------------------------------| :------------| :----------------------------------------------------------------------------------------------------------------------------------|
| **I. Phân tích Yêu cầu & Tài liệu (Iter 3)** | 3 Tuần | Hoàn thiện tài liệu SRS (v2) và SDS (v2), đề xuất, đặt nền tảng cho vai trò mở rộng và RAG. |
| **II. Thiết kế & Mở rộng (Iter 4)**  | 3 Tuần | Phát triển cốt lõi của pipeline RAG, triển khai hệ thống Vai trò Người dùng (Guest/Free/VIP), và cấu hình cơ sở hạ tầng AWS cốt lõi. |
| **III. Tích hợp AWS & Kiểm thử (Iter 5)** | 3 Tuần | Triển khai hoàn chỉnh trên AWS, kiểm thử đầu cuối, và tối ưu hóa hiệu suất.                                                          |
| **IV. Đánh giá & Bàn giao (Handoff)** | Tiếp diễn | Tối ưu hóa, báo cáo hiệu suất và chuẩn bị phát hành beta cuối cùng. |

### Yêu cầu kỹ thuật & sản phẩm bàn giao theo Iter

**Iter 3 – Tích hợp RAG & Thiết kế lại hệ thống (Tuần 1–2–3)**

**Mục tiêu:** Phân tích lại các yêu cầu, hoàn thiện kiến trúc AWS, xác định các luồng trải nghiệm người dùng và tạo mẫu thiết kế RAG.

| Hạng mục | Các nhiệm vụ chính | Trách nhiệm |
| :-------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------- |
| **Tài liệu Hệ thống**  | Xem xét và cập nhật kiến trúc hiện có. Viết **SRS v2** (Chức năng & Phi chức năng) và **SDS v2**.                                                                                          | SE          |
| **UX & Luồng Vai trò** | Thiết kế các luồng người dùng chi tiết cho quá trình chuyển đổi **Khách/Miễn phí/VIP**. Xác định giới hạn chức năng cho mỗi vai trò. Đề xuất **Giao diện nâng cấp VIP**.                   | SE          |
| **Kiến trúc RAG**      | Đề xuất cơ chế RAG (nguồn dữ liệu, pipeline, lưu trữ embedding). Thiết kế pipeline RAG nguyên mẫu: văn bản -\> embedding -\> chỉ mục. **Thu thập Dữ liệu** (Tarot, Horoscope, Numerology). | AI          |
| **Cơ sở hạ tầng AWS**  | Hoàn thiện **Sơ đồ Kiến trúc AWS** (Amplify, Lambda, DynamoDB, Cognito, S3). Tính toán **Ước tính Chi phí** tập trung vào các tùy chọn Miễn phí (Free-tier) và Serverless.                 | SE, AI      |

**Sản phẩm Bàn giao:** Hoàn thành SRS v2, SDS v2, Sơ đồ Kiến trúc AWS kèm Ước tính Chi phí, tài liệu Luồng UX/Vai trò và thiết kế nguyên mẫu RAG.

**Iter 4 – Vai trò Người dùng & Pipeline RAG Sản xuất (Tuần 4–5–6)**

**Mục tiêu:** Triển khai hệ thống ủy quyền người dùng và tích hợp corpus dữ liệu RAG nền tảng.

| Hạng mục                  | Các Nhiệm vụ Chính                                                                                                                                              | Trách nhiệm |
| :-----------------------| :-------------------------------------------------------------------------------------------------------------------------------------------------------------| :--------- |
| **Xác thực & Vai trò**    | Thiết kế lược đồ người dùng (khách, miễn phí, vip). Tích hợp **AWS Cognito** vào frontend. Xác định quyền truy cập API dựa trên vai trò người dùng.             | SE          |
| **Chức năng VIP**         | Triển khai logic cho việc **giới hạn số lần trò chuyện, rút bài Tarot,** và chế độ xem Chiêm tinh chi tiết. Triển khai mô hình định giá và màn hình nâng cấp.   | SE          |
| **Chuẩn bị Sản xuất RAG** | Làm sạch và chuẩn hóa dữ liệu. Triển khai truy xuất ngữ cảnh theo chủ đề (tình yêu, sự nghiệp, cung hoàng đạo). Xây dựng và lưu trữ **Corpus ban đầu trên S3**. | AI          |
| **Tối ưu hóa AI**         | Tinh chỉnh mô hình embedding và kích thước chunk. Viết script cho **cập nhật dữ liệu tự động** (S3 -\> index).                                                  | AI          |

**Sản phẩm Bàn giao:** Hệ thống vai trò người dùng (Guest/Free/VIP) hoạt động đầy đủ được tích hợp với Cognito. Các điểm cuối API RAG mock hoạt động trên môi trường cục bộ/phát triển, cho phép kiểm thử logic truy cập VIP.

**Iter 5 – Triển khai AWS & Đánh giá (Tuần 7 – 8 – 9)**

**Mục tiêu:** Hoàn thành triển khai đầy đủ trên đám mây, thực hiện kiểm thử toàn diện và chuẩn bị cho phát hành công khai.

| Hạng mục | Các Nhiệm vụ Chính | Trách nhiệm |
| :------------------------| :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------- |
| **Cơ sở hạ tầng AWS**      | Cấu hình cuối cùng của **App Runner + Lambda**. Triển khai **DynamoDB, S3, Cognito**. Tạo **Chính sách IAM** tối thiểu.                                                                  | SE          |
| **Giám sát & Ghi nhật ký** | Thiết lập **CloudWatch** cho việc ghi nhật ký của Lambda và Cognito. Tạo bảng điều khiển để giám sát mức sử dụng và chi phí.                                                             | SE          |
| **Tinh chỉnh Mô hình AI**  | Tối ưu hóa **lời nhắc LLM (LLM prompt)** và pipeline RAG. Triển khai các cơ chế **kiểm soát token LLM** để quản lý chi phí.                                                              | AI          |
| **Kiểm thử & Đánh giá**    | Viết **các trường hợp kiểm thử đầu cuối (end-to-end test cases)**. Kiểm thử toàn diện các vai trò, giới hạn VIP và độ chính xác của RAG. Báo cáo về độ chính xác của AI (trước/sau RAG). | AI          |

**Sản phẩm Bàn giao:** Hệ thống ổn định chạy trên cơ sở hạ tầng AWS. Báo cáo hiệu suất, chi phí và kiểm thử (**AWS Service Cost and Performance Sheet**). Sẵn sàng cho việc kiểm thử Beta công khai.

## 5. Mốc thời gian & Các cột mốc 

Dự án SorcererXStreme sẽ được thực hiện trong **khoảng thời gian phát triển tập trung 9 tuần** theo mô hình Agile-Iterative để nhanh chóng cung cấp một MVP với các tính năng chính như **RAG** và **hệ thống người dùng VIP**.

### Mốc thời gian Dự án

| Iter  | Thời gian | Tuần | Trọng tâm chính | Các sản phẩm bàn giao chính |
| :-------------------------------------- | :-------| :-----------| :------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Iter 3: Thiết kế lại & Nguyên mẫu RAG** | 3 Tuần    | 1 – 2 **–** 3 | **Thiết kế Nền tảng & Tài liệu**        |- **SRS v2** và **SDS v2, Đề án** được hoàn thiện.<br> - **Sơ đồ Kiến trúc AWS** kèm Ước tính Chi phí đã sẵn sàng. <br> - Dữ liệu RAG được thu thập và pipeline ban đầu được thiết kế.             |
| **Iter 4: Vai trò & Hệ thống VIP**        | 3 Tuần    | 4 – 5 – 6     | **Triển khai Logic Cốt lõi & Ủy quyền** | - **AWS Cognito** được tích hợp để xác thực người dùng. <br> - Logic vai trò **Khách/Miễn phí/VIP** đầy đủ được triển khai và có thể kiểm thử. <br> - Corpus dữ liệu RAG được xây dựng trên S3.       |
| **Iter 5: Triển khai AWS & QA** | 3 Tuần    | 7 – 8 – 9     | **Triển khai Đám mây & Ổn định** | - Hệ thống chạy **ổn định trên AWS**. <br> - Hoàn thành kiểm thử đầu cuối đầy đủ. **AWS Cost and Performance Sheet** được hoàn thiện. <br> - Sẵn sàng cho Phát hành Beta. |

## 6. Ước tính ngân sách 
Dự án được chúng tôi giả định mức sử dụng thấp cho môi trường Demo và MVP (khoảng 5.000 yêu cầu/tháng).

### Chi phí Cơ sở Hạ tầng

| Layer | AWS Service | Purpose | Estimated Monthly Cost (USD) - Paid |
| :---: | :---| :--- | :---: |
| **I. COMPUTE & API** | | | |
| 1 | **AWS Lambda** | Backend Logic (RAG, Compute) | $0.22 |
| 2 | **Amazon API Gateway** | Synchronous Request Gateway | $0.03 |
| 3 | **AWS App Runner** | Host Frontend (Next.js) | $12.60 |
| **II. DATA & STORAGE** | | | |
| 4 | **RDS for PostgreSQL** | Relational Data/Profiles | $37.14 |
| 5 | **Amazon DynamoDB** | Chat History/Rate Limiting | $0.39 |
| 6 | **Amazon S3** | RAG Knowledge Base/Assets | $0.88 |
| **III. AI & SECURITY** | | | |
| 7 | **Amazon Bedrock** | LLM/Content Generation | $7.87 |
| 8 | **Amazon Cognito** | Authentication/User Roles (MAUs) | $0.00 |
| 9 | **Secrets Manager** | Store Master Keys (Fixed Cost) | $0.01 |
| **IV. ASYNC & MONITORING** | | | |
| 10 | **EventBridge Scheduler** | Daily Horoscope Trigger | $0.00 |
| 11 | **Amazon SQS** | Notification Queue | $0.01 |
| 12 | **Amazon SES** | Email Delivery | $0.24 |
| 13 | **Amazon CloudWatch** | Logs/Metrics/Alarms | $6.23 |
| 14 | **Amazon SNS** | Alert Notifications | $0.00 |

Link ước tính ngân sách: https://drive.google.com/file/d/1B7qVuUHAq4rsdDJjaa-wgAi8MGUIq4Ip/view?usp=sharing

### Tổng Chi Phí Dự Án: **$85.59/month**

## 7. Đánh giá rủi ro 


| Rủi ro | Tác động   | Xác suất   | Chiến lược giảm thiểu |
| :---------------------------------| :-------- | :-------- | :--------------------------------------------------------------------------------------------------------------------------------------------|
| **Ảo giác LLM** | Cao | Trung bình | Triển khai **Bộ kiểm tra Thực tế RAG (RAG Fact Checker)**; sử dụng LLM chất lượng cao; căn cứ câu trả lời vào các nguồn đã được xác minh.      |
| **Vượt chi phí LLM** | Cao | Trung bình | Thiết lập **Cảnh báo Ngân sách AWS (AWS Budget Alerts)**; triển khai **kiểm soát token**; sử dụng mô hình LLM phân tầng (Miễn phí so với VIP). |
| **Độ trễ Truy xuất RAG**            | Trung bình | Trung bình | Tối ưu hóa việc lập chỉ mục RAG (FAISS); tối ưu hóa kích thước chunk và lựa chọn mô hình embedding.                                            |
| **Vi phạm Bảo mật**                 | Cao        | Thấp       | Sử dụng **Cognito** để xác thực và **Secret Manager** để xử lý thông tin xác thực.                                                             |

## 8. Kết quả mong đợi

### Cải tiến kỹ thuật

*   **Độ chính xác theo thời gian thực:** Tích hợp RAG giảm đáng kể "ảo giác" của AI, nâng cao độ tin cậy của các luận giải.
*   **Khả năng mở rộng:** Kiến trúc serverless AWS đảm bảo khả năng mở rộng tự động để xử lý lưu lượng truy cập đáng kể của người dùng.

### Giá trị Dài hạn

*   **Khả năng lợi nhuận:** Mô hình đăng ký VIP tạo ra một con đường rõ ràng, ổn định để tạo doanh thu.
*   **Nền tảng dữ liệu:** Một cơ sở tri thức huyền học độc quyền, đã được xác minh (corpus RAG) được thiết lập như một tài sản có giá trị, có thể tái sử dụng.
*   **Mở rộng tương lai:** Kiến trúc AWS linh hoạt (Lambda, Bedrock) dễ dàng nâng cấp cho các ứng dụng di động hoặc tính năng trò chuyện bằng giọng nói.


