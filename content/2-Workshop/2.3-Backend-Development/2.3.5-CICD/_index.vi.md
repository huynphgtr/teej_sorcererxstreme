---
title : "Tự động hóa CI/CD"
weight : 5
chapter : false
pre : " <b> 2.3.5 </b> "
---

Mục tiêu của phần này là thiết lập quy trình để mỗi khi code được đẩy lên nhánh `main`, toàn bộ kiến trúc Serverless của bạn sẽ được tự động **Build, Test, và Deploy** lên AWS mà không cần thao tác thủ công từ máy cá nhân.

### 1. Chuẩn bị Secret trên GitHub Repo 

Đây là bước quan trọng nhất để cấp quyền cho GitHub Actions truy cập vào AWS.

| Secret Name | Vai trò | Ghi chú và Thao tác |
| :--- | :--- | :--- |
| `AWS_ACCESS_KEY_ID` | Key truy cập AWS. | **RẤT QUAN TRỌNG:** Sử dụng **IAM User** có quyền hạn tối thiểu (không phải Admin) để chỉ có thể tạo/cập nhật các tài nguyên Lambda, API Gateway, và đọc SSM. |
| `AWS_SECRET_ACCESS_KEY` | Secret Key tương ứng. | (Giữ nguyên) |
| `SERVERLESS_ACCESS_KEY` | Key Serverless Dashboard. | Chỉ cần thiết nếu bạn sử dụng các tính năng quản lý của Serverless Dashboard. Nếu không, có thể bỏ qua. |

### 2. File Workflow `.github/workflows/deploy.yml` 

File này định nghĩa các bước mà GitHub Actions sẽ thực hiện. Chúng ta sẽ thêm bước **Build** và loại bỏ các biến môi trường không cần thiết cho lệnh `deploy`.

```yaml
name: Deploy Backend CI/CD via Serverless Framework

on:
  push:
    branches: [ main ] # Kích hoạt khi push lên nhánh main
  workflow_dispatch: # Kích hoạt thủ công từ GitHub UI

jobs:
  deploy:
    runs-on: ubuntu-latest
    # Cấp quyền cho GitHub Actions
    permissions:
      id-token: write 
      contents: read
      
    steps:
      - uses: actions/checkout@v4 # 1. Lấy mã nguồn
      
      - name: Setup Node 20
        uses: actions/setup-node@v3
        with: 
          node-version: '20'
          cache: 'npm' # Kích hoạt cache để tăng tốc cài đặt

      - name: Install Dependencies
        run: npm ci # Dùng npm ci để đảm bảo tính nhất quán (từ package-lock.json)

      # --- CẤU HÌNH PRISMA & BUILD CODE ---
      - name: Generate Prisma Client (tải binary Linux)
        # Tải binary rhel-openssl-3.0.x (cần thiết cho Lambda)
        run: npx prisma generate
        
      - name: Build TypeScript Code (tsc -> dist)
        # Chạy lệnh biên dịch (giả định script "build": "tsc" có trong package.json)
        run: npm run build 

      # --- CẤU HÌNH AWS ---
      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ap-southeast-1

      # --- TRIỂN KHAI CUỐI CÙNG ---
      - name: Deploy Serverless
        # Dùng npx để gọi serverless CLI
        run: npx serverless deploy
        env:
          # Chỉ định rõ stage triển khai. 
          SLS_STAGE: dev 
          # [Lưu ý]: Biến DATABASE_URL đã được xử lý bằng placeholder hoặc SSM
          DATABASE_URL: "placeholder" 
```

### 3. Quy trình CI/CD 

1.  **Code Commit:** Bạn phát triển code và thực hiện `git push` lên GitHub.
2.  **Kích hoạt:** GitHub Actions tự động kích hoạt workflow `deploy.yml`.
3.  **Build & Package:** Runner Ubuntu cài đặt dependencies, chạy `npx prisma generate` (tải binary Linux), và biên dịch code.
4.  **AWS Authentication:** `aws-actions/configure-aws-credentials` đăng nhập vào AWS bằng Secret Key của bạn.
5.  **Provisioning:** Lệnh `npx serverless deploy` đọc `serverless.yml`, kết nối **SSM Parameter Store** để lấy các Key bí mật, và ra lệnh cho **CloudFormation** xây dựng/cập nhật toàn bộ hạ tầng Lambda và API Gateway.