---
title: "Tối ưu hóa & Triển khai Nâng cao"
weight: 5
chapter: false
pre: " <b> 2.2.5. </b> "
---


Sau khi đã deploy thành công ứng dụng **SorcererXtreme** lên môi trường Internet, công việc của chúng ta chưa dừng lại. Để sản phẩm thực sự "Production-Ready", chúng ta cần thực hiện các bước tinh chỉnh chuyên sâu.

### 1. Tên miền riêng (Custom Domain)

Mặc định, AWS Amplify cung cấp một đường dẫn khá dài và khó nhớ (ví dụ: `main.d12345.amplifyapp.com`). Việc thiết lập tên miền riêng không chỉ giúp ứng dụng chuyên nghiệp hơn mà còn cải thiện độ tin cậy.

**Quy trình thực hiện:**

1.  **Mua tên miền:** Bạn có thể mua trực tiếp trên **Amazon Route 53** hoặc các nhà cung cấp khác (Namecheap, GoDaddy).
2.  **Cấu hình trong Amplify:**
    *   Vào **App settings** > **Domain management**.
    *   Nhấn **Add domain** và nhập tên miền của bạn (ví dụ: `sorcererxtreme.vn`).
3.  **Xác thực DNS:**
    *   Nếu mua trên Route 53: Amplify tự động cấu hình (Zero-config).
    *   Nếu mua bên ngoài: Amplify sẽ cung cấp bản ghi CNAME để bạn thêm vào trang quản lý DNS của nhà cung cấp.
4.  **SSL/TLS:** Amplify sẽ tự động cấp phát và quản lý chứng chỉ SSL miễn phí, đảm bảo ổ khóa xanh (HTTPS) cho website.

### 2. Quản lý Biến môi trường (Environment Variables)

Trong quá trình phát triển, chúng ta thường dùng file `.env.local` để chứa các Key nhạy cảm. Tuy nhiên khi deploy, các file này không được đẩy lên Git.

**Tại sao quan trọng?**
*   **Bảo mật:** Giấu kín các khóa API (như API Gateway Endpoint, Cognito User Pool ID) khỏi mã nguồn công khai.
*   **Linh hoạt:** Dễ dàng thay đổi cấu hình giữa môi trường Staging và Production mà không cần sửa code.

**Cách thiết lập:**
1.  Truy cập Amplify Console > Chọn App > **Environment variables**.
2.  Nhập các Key tương ứng (ví dụ: `NEXT_PUBLIC_API_URL`, `NEXT_PUBLIC_USER_POOL_ID`).
3.  Trigger lại quá trình Build để biến môi trường có hiệu lực.

### 3. Tối ưu SEO cho Next.js (Search Engine Optimization)

Với một ứng dụng hướng tới người dùng (B2C) như xem bói Tarot, việc xuất hiện trên Google là sống còn. Next.js (App Router) hỗ trợ SEO cực mạnh thông qua **Metadata API**.

**Dynamic Metadata:**
Thay vì chỉ đặt title tĩnh, bạn có thể tạo title động dựa trên lá bài Tarot mà người dùng bốc được:

```tsx
// app/tarot/[cardId]/page.tsx

export async function generateMetadata({ params }) {
  const card = await getTarotCard(params.cardId);
  return {
    title: `Ý nghĩa lá bài ${card.name} | SorcererXtreme`,
    description: `Khám phá thông điệp vũ trụ từ lá bài ${card.name}...`,
    openGraph: {
      images: [card.imageUrl], // Ảnh hiển thị khi share lên Facebook
    },
  }
}
```

### 4. Giám sát & Phân tích (Monitoring & Analytics)

Bạn không thể cải thiện những gì bạn không đo lường. Sử dụng Tab **Monitoring** trong Amplify để theo dõi:

*   **Incoming Traffic:** Số lượng người truy cập theo thời gian thực.
*   **Data Transfer:** Dung lượng băng thông đã sử dụng.
*   **Error Rate (4XX/5XX):** Phát hiện ngay nếu API bị lỗi hoặc link hỏng.
*   **Access Logs:** Tải xuống log truy cập để phân tích hành vi người dùng (họ đến từ quốc gia nào, dùng trình duyệt gì).

---
{{% notice tip %}}
**Front-end Tip:** Luôn kiểm tra điểm số **Lighthouse** (trong Chrome DevTools) sau mỗi lần deploy. Một ứng dụng đẹp nhưng load chậm sẽ khiến người dùng rời bỏ ngay lập tức. Hãy tối ưu ảnh (dùng format WebP/AVIF) và lazy-load các component nặng.
{{% /notice %}}
