---
title : "Quản lí khóa bí mật"
weight : 4
chapter : false
pre : " <b> 2.3.4 </b> "
---

Việc lưu trữ các biến môi trường nhạy cảm (`DATABASE_URL`, `JWT_SECRET`) vào AWS Systems Manager (SSM) Parameter Store là tiêu chuẩn về bảo mật cho các ứng dụng Serverless.

### 1. Chuẩn bị 

Trong `serverless.yml` của bạn, bạn đã sử dụng biến `${self:provider.stage}` (mặc định là `dev` nếu không chỉ định). Để thống nhất với cấu hình đó, chúng ta nên định nghĩa Key theo format **`/my-app/<stage>/<key>`**.

### 2. Chạy Lệnh CLI để Lưu Key

Bạn cần chạy các lệnh này qua **AWS CLI** sau khi đã cấu hình quyền truy cập.

```bash
# LƯU Ý: Thay thế <YOUR_VALUE> bằng giá trị thực tế của bạn.
# Chúng ta sẽ sử dụng stage mặc định là "dev" cho môi trường workshop.

# --- 1. Lưu Connection String của NeonDB ---
# Dùng "SecureString" để mã hóa dữ liệu.
aws ssm put-parameter \
  --name "/my-app/dev/database_url" \
  --value "postgresql://user:pass@ep-xyz.aws.neon.tech/neondb?sslmode=require" \
  --type "SecureString" \
  --overwrite

# --- 2. Lưu JWT Secret ---
aws ssm put-parameter \
  --name "/my-app/dev/jwt_secret" \
  --value "a_very_long_and_secure_jwt_key_314159" \
  --type "SecureString" \
  --overwrite

# --- 3. Lưu Frontend URL (Nếu cần thiết cho CORS) ---
aws ssm put-parameter \
  --name "/my-app/dev/frontend_url" \
  --value "https://your-amplify-app.amplifyapp.com" \
  --type "String" \
  --overwrite
```

### 3. Kiểm tra 

Sau khi lưu, bạn có thể chạy lệnh sau để kiểm tra xem Parameter đã được lưu đúng chưa và Serverless Framework có thể truy cập được không:

```bash
# Lệnh kiểm tra và giải mã giá trị (cần quyền SSM Read)
aws ssm get-parameter \
  --name "/my-app/dev/database_url" \
  --with-decryption
```

> **Ghi chú Quan trọng:** Trong `serverless.yml`, bạn đã định nghĩa:
> `DATABASE_URL: ${ssm:/my-app/prod/database_url}`.
> Để đồng bộ với lệnh trên, bạn cần sửa `prod` thành `dev` trong `serverless.yml` nếu bạn triển khai với stage mặc định, hoặc chạy lệnh CLI với stage `prod` nếu bạn muốn giữ nguyên file `serverless.yml`.


