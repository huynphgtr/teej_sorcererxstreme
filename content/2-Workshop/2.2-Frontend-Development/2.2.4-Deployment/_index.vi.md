---
title: "Thiết lập CI/CD Pipeline với AWS Amplify"
weight: 4
chapter: false
pre: " <b> 2.2.4. </b> "
---

### 1. Quy trình CI/CD

Chúng ta sẽ thiết lập một quy trình tự động hóa hoàn toàn như sau:

![CI/CD Pipeline Diagram](/images/cicd_pipeline_diagram_1764662962948.png)

*   **Source:** Developer push code lên GitHub.
*   **Build:** Amplify tự động phát hiện thay đổi, cài đặt dependencies và build Next.js.
*   **Deploy:** Đẩy code đã build lên hệ thống Hosting toàn cầu.
*   **Verify:** Kiểm tra trang web hoạt động (Health Check).

### 2. Kết nối Repository

1.  Đẩy code lên Git Repository (GitHub/GitLab/CodeCommit).
2.  Trong AWS Amplify Console, chọn **Host web app**.
3.  Kết nối với Repository chứa mã nguồn Frontend.

### 3. Cấu hình Build (Build Specification)

Amplify sử dụng file `amplify.yml` để định nghĩa các bước build.

```yaml
version: 1
frontend:
  phases:
    preBuild:
      commands:
        - npm ci
    build:
      commands:
        - npm run build
  artifacts:
    baseDirectory: .next
    files:
      - '**/*'
  cache:
    paths:
      - node_modules/**/*
```

### 4. Quản lý Biến môi trường (Environment Variables)

Không bao giờ hard-code các giá trị nhạy cảm hoặc thay đổi theo môi trường (như API URL) trong code. Hãy sử dụng Environment Variables trong Amplify.

1.  Vào **App settings** > **Environment variables**.
2.  Thêm biến:
    *   Key: `NEXT_PUBLIC_API_URL`
    *   Value: `https://xyz.execute-api.us-east-1.amazonaws.com/prod`
3.  Trong code Next.js, truy cập bằng `process.env.NEXT_PUBLIC_API_URL`.

*Lưu ý: Các biến bắt đầu bằng `NEXT_PUBLIC_` sẽ được Next.js nhúng vào code Frontend tại thời điểm build.*

### 5. Branch Previews (Pull Request Previews)

Tính năng này cực kỳ hữu ích khi làm việc nhóm.
*   Khi bạn tạo một Pull Request (PR) trên GitHub, Amplify sẽ tự động tạo một môi trường Preview tạm thời (có URL riêng).
*   Team Leader có thể vào URL đó để review tính năng mới trước khi Merge vào nhánh chính.
*   Sau khi Merge, môi trường Preview sẽ tự động bị xóa.

Để bật tính năng này: Vào **App settings** > **Previews** > **Enable previews**.

---

### Chuyện nghề (My Experience)

{{% notice note %}}
**Lỗi Build trên Linux vs Windows**
Máy mình dùng Windows (không phân biệt hoa thường), nhưng Amplify chạy Linux (có phân biệt). Có lần mình import `Component.tsx` nhưng file lại tên là `component.tsx`. Ở máy mình chạy ngon ơ, lên Amplify thì lỗi "File not found".
**Bài học:** Luôn đặt tên file chuẩn (PascalCase cho Component, camelCase cho ultils) và kiểm tra kỹ khi đổi tên file.
{{% /notice %}}

### Kiểm thử & Xác thực (Verification)

**Test Case 1: Trigger Build**
1.  Sửa một dòng text nhỏ trong file `page.tsx`.
2.  Commit và Push lên GitHub: `git push origin main`.
3.  Vào Amplify Console.
4.  *Kết quả mong đợi:* Thấy trạng thái chuyển sang "Provisioning" -> "Building" -> "Deploying".

**Test Case 2: Kiểm tra Log**
1.  Click vào lần build đang chạy.
2.  Mở tab **Frontend**.
3.  *Kết quả mong đợi:* Thấy log xanh (Success) ở các bước.
    ```
    2024-05-20T10:00:00.000Z [INFO]: # Executing command: npm run build
    ...
    2024-05-20T10:01:00.000Z [INFO]: Compiled successfully
    ```
