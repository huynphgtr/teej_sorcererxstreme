---
title : "Kết nối microservices"
weight : 6
chapter : false
pre : " <b> 2.3.6 </b> "
---

## Gọi API Nội bộ (Backend -> AI Service)

Trong kiến trúc Serverless, khi một hàm Lambda (ví dụ: `Chatbot`) cần gọi một dịch vụ khác (`MetaphysicalAPI`), phương pháp tốt nhất là sử dụng HTTP request thông qua API Gateway của dịch vụ đó.

### 1. Nguyên tắc Gọi API

  * **Endpoint:** Luôn gọi thông qua API Gateway Endpoint công khai (hoặc nội bộ nếu cả hai Lambda đều trong VPC và dùng Private API Gateway).
  * **Thư viện:** Sử dụng thư viện HTTP quen thuộc như **`axios`** để quản lý yêu cầu và timeout phía client.
  * **Bảo mật:** Sử dụng IAM Roles hoặc API Keys để xác thực cuộc gọi giữa các dịch vụ nếu API Gateway không hoàn toàn công khai.

### 2. Quản lý Thời gian chờ (Timeout)

Quản lý `timeout` là yếu tố quan trọng nhất để tránh lỗi 504 Gateway Timeout.

  * **API Gateway Limit:** API Gateway có giới hạn thời gian chờ cứng là 29 giây. Mọi request kéo dài hơn 29 giây đều bị cắt và trả về lỗi 504.
  * **Lambda Limit:** Lambda có thể chạy tới 15 phút, nhưng bị giới hạn bởi API Gateway. Do đó, thời gian chờ của Lambda phải được đặt **nhỏ hơn 29 giây** (ví dụ: 25 giây).

| Cấu hình | Vị trí | Giá trị Khuyến nghị | Lý do |
| :--- | :--- | :--- | :--- |
| **Timeout API Gateway** | `serverless.yml` (Provider level) | 29 giây | Giới hạn tối đa của AWS. |
| **Timeout Lambda (Receiver)** | `serverless.yml` (Function level) | 25 giây | Phải nhỏ hơn 29s để Lambda kịp trả lỗi trước khi API Gateway cắt. |
| **Timeout Client (`axios`)** | Code TypeScript | 25,000 ms (25 giây) | Đảm bảo client cắt kết nối trước khi Lambda hết thời gian chờ, cho phép xử lý lỗi phía client gọn gàng. |

### 3. Tối ưu Hiệu suất Lambda 

Nếu AI Service của bạn thực hiện các tác vụ nặng (như RAG, tính toán thiên văn), việc tối ưu tốc độ xử lý là bắt buộc.

  * **Tăng Bộ nhớ (RAM):** Tăng `memorySize` của AI Service Lambda lên mức cao (ví dụ: 1536 MB hoặc 3008 MB). Trên AWS, tăng RAM cũng đồng thời tăng CPU và Network Bandwidth, giúp xử lý nặng nhanh hơn đáng kể, giảm khả năng xảy ra lỗi 504.
  * **Cấu hình Code:** Cấu hình `timeout: 25` trong `serverless.yml` cho cả Backend và AI Service.

### 4. Code mẫu

Đây là hàm gọi AI đã được chỉnh sửa để xử lý các ngoại lệ một cách rõ ràng và tuân thủ nguyên tắc Timeout.

```typescript
import axios from 'axios';

// Giả định AI_SERVICE_URL đã được lấy từ AWS SSM Parameter Store và bơm vào ENV
const AI_SERVICE_URL = process.env.AI_SERVICE_URL; 
const CLIENT_TIMEOUT_MS = 25000; // 25 giây (Dưới giới hạn 29s của API Gateway)

/**
 * Gọi API Gateway của dịch vụ AI để nhận phản hồi.
 * @param prompt Dữ liệu yêu cầu gửi đến AI.
 */
export const callAI = async (prompt: string, userId: string) => {
  if (!AI_SERVICE_URL) {
    throw new Error("AI Service URL is not configured.");
  }
  
  try {
    const res = await axios.post(AI_SERVICE_URL, { 
      prompt,
      userId // Gửi ID người dùng để AI Service có thể log hoặc xử lý VIP
    }, {
      timeout: CLIENT_TIMEOUT_MS // Thiết lập timeout client
    });
    
    // Xử lý response thành công
    return res.data; 

  } catch (error) {
    if (axios.isAxiosError(error) && error.code === 'ECONNABORTED') {
      // Lỗi Timeout phía client
      console.error("AI Timeout Error: Request took too long.");
      throw new Error("AI Service timed out (504). Please try again.");
    }
    
    // Lỗi khác (ví dụ: 4xx, 5xx từ AI Service)
    console.error("AI Service Error:", error.message);
    throw new Error("AI Service unavailable or returned an error.");
  }
};
```



