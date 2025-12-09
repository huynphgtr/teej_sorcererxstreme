---
title: "Đề Án"
weight: 1
chapter: false
pre: " <b> 1. </b> "
---
<img src="/images/logo.jpg" class="img-responsive" style="max-width:300px; display:block; margin:auto;">

# AWS FIRST CLOUD AI JOURNEY – PROJECT PLAN
# TEEJ_SorcererXStreme - FPT University - Ho Chi Minh Campus - SORCERERXSTREME

---

## 0. PHỤ LỤC

1. [BỐI CẢNH VÀ ĐỘNG LỰC](#1-bối-cảnh-và-động-lực)
   * 1.1. [Tóm tắt](#11-tóm-tắt)
   * 1.2. [Tiêu chí thành công của dự án](#12-tiêu-chí-thành-công-của-dự-án)
   * 1.3. [Giả định và tiền đề](#13-giả-định-và-tiền-đề)

2. [KIẾN TRÚC GIẢI PHÁP](#2-kiến-trúc-giải-pháp)
   * 2.1. [Sơ đồ kiến trúc kỹ thuật](#21-sơ-đồ-kiến-trúc-kỹ-thuật)
   * 2.2. [Kế hoạch kỹ thuật](#22-kế-hoạch-kỹ-thuật)
   * 2.3. [Kế hoạch dự án](#23-kế-hoạch-dự-án)
   * 2.4. [Các yếu tố bảo mật](#24-các-yếu-tố-bảo-mật)

3. [HOẠT ĐỘNG VÀ KẾT QUẢ BÀN GIAO](#3-hoạt-động-và-kết-quả-bàn-giao)
   * 3.1. [Hoạt động và kết quả bàn giao](#31-hoạt-động-và-kết-quả-bàn-giao)
   * 3.2. [Nằm ngoài dự án](#32-nằm-ngoài-dự-án)
   * 3.3. [Quy trình đưa vào vận hành](#33-quy-trình-đưa-vào-vận-hành)

4. [PHÂN TÍCH CHI PHÍ THEO DỊCH VỤ](#4-phân-tích-chi-phí-theo-dịch-vụ)

5. [ĐỘI NGŨ](#5-đội-ngũ)

6. [NGUỒN LỰC VÀ ƯỚC TÍNH CHI PHÍ](#6-nguồn-lực-và-ước-tính-chi-phí)

7. [NGHIỆM THU](#7-nghiệm-thu)

---

## 1. BỐI CẢNH VÀ ĐỘNG LỰC

### 1.1 Tóm tắt
<!-- Dự án **SorcererXStreme AI** được phát triển nhằm giải quyết vấn đề phân mảnh, thiếu kiểm chứng và thiếu tính cá nhân hóa của các thông tin tâm linh trên internet hiện nay. Người dùng hiện gặp khó khăn khi so sánh các trường phái Đông - Tây cũng như tiếp cận các nội dung có chiều sâu.

SorcererXStreme là nền tảng luận giải tâm linh hợp nhất, sử dụng công nghệ AI tạo sinh (Generative AI) với lõi **RAG (Retrieval-Augmented Generation)** để cung cấp các luận giải dựa trên dữ liệu đã xác minh từ Chiêm tinh học, Tarot, Thần số học và Tử vi Phương Đông. Giải pháp đảm bảo độ tin cậy cao, giảm thiểu "ảo giác" của AI và cung cấp trải nghiệm trò chuyện trực tiếp, sâu sắc. -->

#### 1. Bối cảnh khách hàng & Vấn đề 

* **Vấn đề:** Các nguồn thông tin tâm linh hiện nay đang bị phân mảnh, thiếu kiểm chứng và thiếu tính cá nhân hóa. Người dùng gặp khó khăn khi tìm kiếm các luận giải sâu sắc, đáng tin cậy hoặc so sánh các trường phái tri thức Đông - Tây (ví dụ: Tử vi vs. Chiêm tinh).
* **Động lực:** Nhu cầu cấp thiết của khách hàng hiện tại là xây dựng một nền tảng hợp nhất có khả năng cung cấp các nội dung được kiểm định về mặt tri thức, đồng thời duy trì khả năng mở rộng và tối ưu chi phí vận hành.


#### 2. Mục tiêu kinh doanh & Kỹ thuật 

<table>
  <thead>
    <tr>
      <th style="width: 20%;">Loại Mục tiêu</th>      
      <th style="width: 70%;">Chi tiết</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Kinh doanh</strong></td>
      <td> - Cung cấp nội dung có độ tin cậy và chiều sâu vượt trội so với các dịch vụ hiện tại. <br> - Tạo doanh thu từ mô hình dịch vụ trả phí.</td>
    </tr>
    <tr>
      <td><strong>Kỹ thuật</strong></td>
      <td>- Đảm bảo độ tin cậy AI :Áp dụng lõi RAG để giảm thiểu "ảo giác" của AI. <br> - Khả năng mở rộng: Xây dựng kiến trúc AWS Serverless (Lambda, API Gateway) để dễ dàng đáp ứng lưu lượng truy cập lớn và tối ưu chi phí vận hành (Pay-per-use).</td>
    </tr>
  </tbody>
</table>


#### 3. Trường hợp sử dụng 

Các chức năng chính mà dự án sẽ hỗ trợ người dùng:

* **AI Chatbot tương tác sâu:** Trò chuyện trực tiếp với AI, có khả năng duy trì ngữ cảnh và kết hợp nhiều trường phái trong một phiên sử dụng duy nhất.
* **Luận giải cá nhân hóa:** Cung cấp các báo cáo chuyên sâu dựa trên dữ liệu đầu vào (ngày sinh, nơi sinh, giờ sinh).
* **Thông báo tự động:** Gửi các thông báo định kỳ qua email.

#### 4. Tóm tắt dịch vụ tư vấn

Dịch vụ chuyên nghiệp sẽ được cung cấp để đạt được các mục tiêu trên bao gồm:

* **Thiết kế Kiến trúc Serverless:** Xây dựng kiến trúc đa mô hình và thiết lập luồng RAG trên AWS Bedrock.
* **Tối ưu hóa Chi phí & Hiệu suất:** Tinh chỉnh các hàm Lambda và thiết lập bảo mật qua SSM Parameter Store.
* **Tự động hóa CI/CD:** Triển khai toàn bộ quy trình phát triển và triển khai tự động (IaC) bằng Serverless Framework và GitHub Actions.

### 1.2 Tiêu chí thành công của dự án
Sự thành công của dự án sẽ được đánh giá dựa trên các tiêu chí định lượng và định tính sau:
* **Độ tin cậy dữ liệu:** Hệ thống RAG hoạt động chính xác, giảm thiểu ảo giác AI, cung cấp nguồn dẫn chứng minh bạch cho các luận giải.
* **Hợp nhất tri thức:** Thành công trong việc tích hợp dữ liệu huyền học Đông và Tây vào một nền tảng duy nhất.
* **Hiệu quả kinh doanh:** Triển khai thành công mô hình phân tầng (Free/VIP) để tạo dòng doanh thu ổn định.
* **Tối ưu chi phí:** Hệ thống vận hành trên kiến trúc Serverless với chi phí ước tính khoảng $9.06/tháng cho môi trường Demo.
* **Khả năng mở rộng:** Hệ thống tự động mở rộng (Auto-scaling) để xử lý lưu lượng truy cập lớn mà không cần can thiệp thủ công.

### 1.3 Giả định và tiền đề
Dự án được thực hiện dựa trên các giả định và ràng buộc sau:
* **Phụ thuộc công nghệ:** Dự án phụ thuộc vào tính sẵn sàng và ổn định của các dịch vụ AWS (Bedrock, Lambda, API Gateway).
* **Dữ liệu:** Giả định rằng dữ liệu đầu vào cho RAG (sách, tài liệu huyền học) là sạch, có bản quyền hoặc thuộc phạm vi sử dụng hợp lệ.
* **Rủi ro:** Có rủi ro về "ảo giác" của LLM dù đã dùng RAG, cần có cơ chế Fact Checker.
* **Ràng buộc chi phí:** Ngân sách vận hành được tối ưu hóa chặt chẽ.

---

## 2. KIẾN TRÚC GIẢI PHÁP

### 2.1 Sơ đồ kiến trúc kỹ thuật
Kiến trúc đề xuất là **Hybrid Serverless** trên AWS, bao gồm các lớp: Edge & Auth, API & Routing, Compute, Data, AI/ML và Async Monitoring.

![Architecture Diagram](/images/High_Level_System_Architecture.drawio(2).png)

**Các thành phần chính:**
* **Frontend:** AWS Amplify (Next.js).
* **Auth:** Amazon Cognito.
* **Backend:** AWS Lambda, Amazon API Gateway.
* **Database:** NeonDB (PostgreSQL) làm DB chính, DynamoDB cho lịch sử, Pinecone cho Vector Search.
* **AI Core:** Amazon Bedrock (LLM & Embeddings), S3 (RAG Docs).

### 2.2 Kế hoạch kỹ thuật
Đội ngũ dự án sẽ phát triển và triển khai hệ thống theo quy trình kỹ thuật sau:
* **Scripts & IaC:** Sử dụng **Serverless Framework** để sinh ra CloudFormation template, đảm bảo việc triển khai hạ tầng (IaC) có thể lặp lại và nhất quán.
* **CI/CD:** Sử dụng **GitHub Actions** để tự động hóa quy trình Build, Test và Deploy các hàm Lambda và API Gateway.
* **RAG Pipeline:** Thiết lập luồng xử lý dữ liệu: Upload tài liệu lên S3 -> Lambda Trigger -> Tạo Embedding (Bedrock) -> Lưu vào Pinecone Vector DB.

### 2.3 Kế hoạch dự án
Dự án áp dụng mô hình **Agile-Iterative** trong 9 tuần, chia làm 3 giai đoạn (Iteration) chính:
* **Iter 3 (Tuần 1-3):** Thiết kế lại hệ thống, hoàn thiện tài liệu SRS/SDS và dựng Prototype cho RAG pipeline.
* **Iter 4 (Tuần 4-6):** Tích hợp AWS Cognito, phát triển logic phân quyền (Guest/VIP), xây dựng kho dữ liệu (Corpus) trên S3.
* **Iter 5 (Tuần 7-9):** Triển khai toàn diện lên AWS, kiểm thử End-to-End (QA), tối ưu hóa chi phí và hiệu năng.

### 2.4 Các yếu tố bảo mật
Bảo mật được thiết kế theo mô hình "Defense in Depth":
* **Identity & Access:** Sử dụng Amazon Cognito để quản lý định danh và phân quyền người dùng (User Roles).
* **Data Protection:** Các khóa bí mật (API Keys, DB Credentials) được lưu trữ an toàn trong **AWS Systems Manager Parameter Store**, không lưu cứng trong code.
* **Network Security:** API Gateway đóng vai trò là cửa ngõ duy nhất.
* **Monitoring:** Sử dụng CloudWatch để ghi log và giám sát các hành vi bất thường.

---

## 3. HOẠT ĐỘNG VÀ KẾT QUẢ BÀN GIAO

### 3.1 Activities and deliverables

| Giai đoạn triển khai | Timeline | Hoạt động | Milestones | Ngày hoàn thành |
| :--- | :--- | :--- | :--- | :--- |
| **Design & Prototype** | Tuần 1-3 | - Thiết kế kiến trúc AWS.<br>- Thu thập dữ liệu RAG.<br>- Viết tài liệu SRS/SDS. | - Sơ đồ kiến trúc & Bảng chi phí.<br>- RAG Pipeline Prototype.<br>- Tài liệu đề án. | 12/10/-01/11/2025 |
| **Development (Core)** | Tuần 4-6 | - Tích hợp Cognito.<br>- Code Backend (Lambda).<br>- Xây dựng Vector DB. | - Hệ thống Auth hoạt động.<br>- API hoàn chỉnh cho VIP/Free.<br>- RAG Knowledge Base sẵn sàng. | 02/11-22/11/2025 |
| **Deployment & QA** | Tuần 7-9 | - Cấu hình GitHub Actions.<br>- Deploy lên môi trường Prod.<br>- Load Test & Pen Test. | - Hệ thống Live trên AWS.<br>- Báo cáo kiểm thử.<br>- Tài liệu hướng dẫn sử dụng. | 23/11-09/12/2025 |

### 3.2 Nằm ngoài dự án
Các hạng mục sau nằm ngoài phạm vi của dự án (MVP) hiện tại:
* Phát triển ứng dụng di động (Mobile App - iOS/Android).
* Tính năng Voice Chat thời gian thực (trò chuyện bằng giọng nói).

### 3.3 Quy trình đưa vào vận hành
Phiên bản hiện tại là MVP (Minimum Viable Product). Để đưa vào sản xuất quy mô lớn, cần thực hiện thêm các bước:
* **Mở rộng tính năng:** Nâng cấp kiến trúc Lambda và Bedrock để hỗ trợ React Native Mobile App hoặc Voice Chat trong tương lai.
* **Operational Excellence:** Thiết lập AWS CloudWatch Alarms chi tiết hơn để giám sát lỗi và độ trễ.
* **Tối ưu RAG:** Tinh chỉnh kích thước chunk và chiến lược truy xuất để giảm độ trễ phản hồi.

---

## 4. PHÂN TÍCH CHI PHÍ THEO DỊCH VỤ

Chi phí ước tính cho môi trường Demo (~5.000 requests/tháng) là **$9.06/tháng**.

Link chi tiết: [Bảng ước tính chi phí](https://drive.google.com/file/d/1Cy0ivN1wIOJ8DthIOE4cYYFG1BzESt5m/view?usp=sharing)

| Layer | AWS Service | Purpose | Cost/Month |
| :--- | :--- | :--- | :--- |
| **Compute & API** | AWS Lambda, API Gateway, Amplify | Backend Logic & Hosting | ~$2.62 |
| **Data & Storage** | DynamoDB, S3 | Chat History & RAG Storage | ~$0.92 |
| **AI & Security** | Bedrock, Cognito, Parameter Store | LLM Generation & Auth | ~$2.65 |
| **Async & Monitoring** | SES, CloudWatch, EventBridge | Email & Logging | ~$2.88 |
| **Tổng** | | | **$9.06** |

---

## 5. ĐỘI NGŨ

### Chịu trách nhiệm tổng thể

| Tên | Chức danh | Mô tả | Email |
| :--- | :--- | :--- | :--- |
| *Nguyễn Gia Hưng* | *Head of Architecture Solution* | *Thiết kế và phát triển các nền tảng đám mây gốc và phi máy chủ* | *hunggia@amazon.com* |

### Các bên liên quan

| Tên | Chức danh | Mô tả | Email |
| :--- | :--- | :--- | :--- |
| *Đình Quang Sáng* | PQHDN | Đánh giá & Định hướng | *SangDQ6@fe.edu.vn* |

### Đại diện hỗ trợ

| Tên | Chức danh | Mô tả | Email |
| :--- | :--- | :--- | :--- |
| *Văn Hoàng Kha*| Cloud Security Engineer, Co-founded and led Viet-AWS | Thực thi các hướng kỹ thuật về Bảo mật đám mây và DevSecOps | *khavan.work@gmail.com* |


### Đội ngũ thực hiện dự án

| Tên | Chức danh | Mô tả | Email |
| :--- | :--- | :--- | :--- |
| *Trần Phương Huyền* | Leader + Backend Dev | Quản lý dự án + Phát triển Backend | *tranphuonghuyen2005@gmail.com* |
| *Nguyễn Lâm Anh* | AI Dev | Phát triển AI | *nguyenla110505@gmail.com* |
| *Nguyễn Văn Linh* | AI Dev | Phát triển AI | *nguyenvanlinh.1710.it@gmail.com* |
| *Bùi Nguyễn Tấn Khang* | Frontend Dev | Phát triển Frontend | *tankhang6a6@gmail.com* |


---

## 6. NGUỒN LỰC VÀ ƯỚC TÍNH CHI PHÍ

### Định mức đơn giá nhân sự
*(Đơn giá nhân sự ước tính dựa trên định mức dự án sinh viên/học thuật)*

| Nguồn lực / Vai trò | Trách nhiệm | Đơn giá (USD) / Giờ |
| :--- | :--- | :--- |
| **Solution Architects** | - Thiết kế kiến trúc tổng thể (Hybrid Serverless, RAG Flow).<br>- Lựa chọn dịch vụ AWS tối ưu (Bedrock, Lambda, Pinecone).<br>- Đảm bảo các yêu cầu phi chức năng (bảo mật, độ trễ, chi phí). | **2.3** |
| **Software Engineers** | - Phát triển Frontend (Next.js/Amplify) và tích hợp Cognito.<br>- Xây dựng Backend API (Lambda) để xử lý logic người dùng.<br>- Quản lý lưu trữ dữ liệu Chat History (DynamoDB). | **0.7** |
| **AI Engineers** | - Tinh chỉnh Prompt cho Bedrock để luận giải Tarot/Tử vi.<br>- Xây dựng luồng RAG: Chunking tài liệu, Embeddings, Vector Search.<br>- Đánh giá độ chính xác và giảm thiểu ảo giác (Hallucination) của AI. | **0.7** |

### Ước tính giờ công & chi phí theo giai đoạn
*(Ước tính nỗ lực cho 3 Iterations - 9 tuần)*

| Giai đoạn Dự án | Vai trò | Giờ công | Chi phí (USD) | Tổng Chi phí Giai đoạn |
| :--- | :--- | :--- | :--- | :--- |
| **Iter 3: Design & Prototype**<br>*(Tuần 1-3)* | **Solution Architect**<br>Software Engineer<br>AI Engineer | 30<br>30<br>30 | $69.0<br>$21.0<br>$21.0 | **$111.0** |
| **Iter 4: Core Development**<br>*(Tuần 4-6)* | Solution Architect<br>**Software Engineer**<br>**AI Engineer** | 20<br>80<br>80 | $46.0<br>$56.0<br>$56.0 | **$158.0** |
| **Iter 5: Deploy & QA**<br>*(Tuần 7-9)* | Solution Architect<br>Software Engineer<br>AI Engineer | 10<br>60<br>40 | $23.0<br>$42.0<br>$28.0 | **$93.0** |
| **TỔNG CỘNG** | | **380** | | **$362.0** |

### Phân bổ đóng góp chi phí
*(Phân bổ trách nhiệm chi phí giữa các bên liên quan)*

| Bên tham gia | Giá trị Đóng góp (USD) | Tỷ lệ Đóng góp trên Tổng số |
| :--- | :--- | :--- |
| **Partner (Team)** | **$362.0** | **97.5%** (Chi phí nhân sự tự đóng góp) |
| **AWS** | **~$9.06/tháng** | **2.5%** (Chi phí hạ tầng Cloud ước tính) |
| **Customer** | **$0** | **0%** (Dự án học thuật/MVP) |

---

## 7. NGHIỆM THU

Việc nghiệm thu dự án **SorcererXStreme** sẽ không chỉ dựa trên việc hoàn thành tính năng, mà còn căn cứ vào độ ổn định của hệ thống và chất lượng đầu ra của AI.

### 7.1. Gói bàn giao 
Sản phẩm được coi là sẵn sàng để nghiệm thu khi đội dự án cung cấp đầy đủ các hạng mục sau:
* **Mã nguồn (Source Code):** Repository chứa toàn bộ code Backend (Lambda), Frontend (Next.js), và Infrastructure as Code (Serverless Framework).
* **Tài liệu kỹ thuật (Documentation):** Bao gồm SRS, SDS, API Documentation và hướng dẫn triển khai (Deployment Guide).
* **Môi trường vận hành (Live Environment):** Đường dẫn (URL) truy cập vào môi trường Demo đã hoạt động ổn định trên AWS.
* **Báo cáo chất lượng (Quality Report):** Kết quả kiểm thử (Test Cases) và báo cáo đánh giá độ chính xác của mô hình RAG.

### 7.2. Quy trình nghiệm thu 
Quy trình sẽ diễn ra theo trình tự 4 bước:
1.  **Trình diễn (Live Demo):** Đội dự án demo trực tiếp các luồng người dùng (User Flow) chính: Đăng ký -> Chọn dịch vụ -> Chat với AI -> Nhận kết quả.
2.  **Kiểm thử chấp nhận (UAT):** Khách hàng/Mentor trực tiếp sử dụng hệ thống (03-05 ngày) để kiểm tra độ chính xác của kiến thức tâm linh và khả năng chịu tải.
3.  **Phản hồi & Khắc phục:** Đội dự án cam kết sửa lỗi nghiêm trọng (Critical) trong 24-48 giờ. Các lỗi nhỏ sẽ được cập nhật trong bản vá sau.
4.  **Xác nhận hoàn thành:** Dự án được nghiệm thu khi không còn lỗi nghiêm trọng và các tính năng cốt lõi hoạt động đúng cam kết.

### 7.3. Điều kiện từ chối 
Sản phẩm sẽ chưa được nghiệm thu nếu:
* **Lỗi triển khai:** Hệ thống Downtime hoặc API lỗi > 10%.
* **Sai lệch nội dung:** AI đưa ra thông tin sai lệch nghiêm trọng hoặc vi phạm quy tắc an toàn.
* **Vượt ngân sách:** Chi phí vận hành thực tế vượt quá ngưỡng cho phép mà không có giải trình hợp lý.