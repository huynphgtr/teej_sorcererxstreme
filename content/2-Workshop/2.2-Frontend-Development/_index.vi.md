---
title: "Phần phát triển Frontend"
weight: 2
chapter: false
pre: " <b> 2.2. </b> "
---

# Xây dựng Frontend Serverless với Next.js & AWS Amplify

## 1. Tổng quan Workshop

Chào mừng bạn đến với Workshop **Xây dựng Frontend Serverless hiện đại** cho dự án **SorcererXtreme**.

Trong dự án này, Frontend đóng vai trò là "gương mặt đại diện", nơi người dùng tương tác trực tiếp với các tính năng huyền bí. Nhiệm vụ của chúng ta là xây dựng một giao diện đẹp, mượt mà và giao tiếp hiệu quả với các dịch vụ AWS phía sau.

## 2. Kiến trúc Frontend (Frontend Architecture)

Chúng ta sẽ tập trung vào kiến trúc của phần Client (Frontend) và các điểm kết nối (Integration Points):

![Frontend Architecture Diagram](/images/frontend_architecture_diagram.png)

### Luồng hoạt động của Frontend:

1.  **Hosting & Delivery:** Code Next.js được lưu trữ và vận hành trên **AWS Amplify**. Người dùng truy cập web thông qua mạng lưới CDN toàn cầu (CloudFront) tích hợp sẵn trong Amplify, đảm bảo tốc độ tải trang cực nhanh.
2.  **Authentication (Xác thực):** Khi người dùng Đăng nhập, Frontend sẽ giao tiếp trực tiếp với **Amazon Cognito**. Cognito trả về một "Token" (giống như tấm vé thông hành).
3.  **API Interaction (Giao tiếp):** Với mỗi yêu cầu (như chatbot hoặc xem bài Tarot), Frontend sẽ gửi Token kèm theo request đến **Amazon API Gateway**.
4.  **Response:** Frontend nhận kết quả JSON từ API và hiển thị lên giao diện (Render UI). Frontend **không cần biết** phía sau API là Database gì hay AI model nào, nó chỉ quan tâm đến đầu vào (Request) và đầu ra (Response).

## 3. Công nghệ Frontend sử dụng (Tech Stack)

Bộ công cụ "vũ khí" của Frontend Developer trong dự án này:

| Công nghệ | Vai trò | Tại sao dùng? |
| :--- | :--- | :--- |
| **Next.js (App Router)** | Framework | Hỗ trợ Server-Side Rendering (SSR) tốt cho SEO, Router mạnh mẽ. |
| **AWS Amplify (Gen 2)** | Platform | Cung cấp Hosting, CI/CD tự động và thư viện kết nối Cloud cực nhanh. |
| **Tailwind CSS** | Styling | Viết CSS nhanh, dễ dàng tùy chỉnh giao diện "Dark Mode" huyền bí. |
| **Framer Motion** | Animation | Tạo hiệu ứng chuyển động mượt mà (như lật bài Tarot 3D). |
| **Amplify UI** | Library | Bộ component có sẵn cho phần Đăng nhập/Đăng ký (Login UI). |
| **Axios / Fetch** | HTTP Client | Dùng để gọi API Gateway. |

## 4. Thời gian & Chi phí ước tính

| Mục | Chi tiết |
| :--- | :--- |
| **Thời gian** | 2-3 giờ mỗi ngày |
| **Chi phí** | **~$9.06/tháng** (Toàn bộ dự án) |

## 5. Nội dung thực hành

Chúng ta sẽ đi qua quy trình phát triển Frontend chuẩn:

1.  [**Chuẩn bị môi trường:**](5.1-Preparation/) Thiết lập Next.js và Amplify.
2.  [**UI Implementation:**](5.2-UI-Implementation/) Code giao diện Chat & Tarot với hiệu ứng động.
3.  [**Integration:**](5.3-Integration/) Tích hợp Login (Cognito) và gọi API (Gateway).
4.  [**CI/CD Pipeline:**](5.4-Deployment/) Đẩy code lên Git và tự động deploy ra Internet.
5.  [**Advanced:**](5.5-Advanced-Deployment/) Cấu hình tên miền riêng và tối ưu SEO.
6.  [**Backend Reference:**](5.6-Backend-Architecture/) Tìm hiểu mô hình RAG.
7.  [**Cleanup:**](5.7-Cleanup/) Dọn dẹp tài nguyên.

---
{{% notice tip %}}
**Tư duy Frontend:** Trong kiến trúc Serverless, Frontend không chỉ là "người hiển thị". Nó còn chịu trách nhiệm về **Bảo mật** (giữ Token an toàn) và **Tối ưu trải nghiệm** (xử lý Loading state khi chờ AI trả lời). Hãy chú ý các điểm này trong bài thực hành!
{{% /notice %}}