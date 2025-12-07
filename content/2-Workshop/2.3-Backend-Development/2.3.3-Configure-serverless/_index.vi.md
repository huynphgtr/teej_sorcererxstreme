---
title : "Cấu hình Serverless Framework"
weight : 3
chapter : false
pre : " <b> 2.3.3 </b> "
---

Đây là bước quan trọng nhất, nơi chúng ta định nghĩa hạ tầng AWS (IaC) và tối ưu gói triển khai (deployment package) cho Lambda.

### 1. File `serverless.yml` 

File cấu hình này bao gồm các phần Bảo mật SSM, Tối ưu Build/Package và IAM Roles cần thiết.

```yaml
# serverless.yml
org: ten_org_cua_ban
service: my-serverless-backend # Đảm bảo service name khớp với thư mục

provider:
  name: aws
  runtime: nodejs20.x
  region: ap-southeast-1
  timeout: 29 # Tối đa của API Gateway (giữ nguyên)
  memorySize: 512 # Cấu hình bộ nhớ Lambda (khuyến nghị cho Prisma)
  
  # --- Cấu hình Mạng VPC (BẮT BUỘC cho NeonDB) ---
  # Nếu NeonDB yêu cầu IP cố định hoặc bạn dùng RDS nội bộ, 
  # Lambda cần cấu hình VPC để kết nối ra ngoài hoặc vào VPC.
  # (Giả định cho NeonDB Public Access, nhưng cần VPC nếu bạn dùng Subnet Private)
  # vpc:
  #   securityGroupIds: [sg-xxxxxxxx]
  #   subnetIds: [subnet-xxxxxx]
  
  # --- Biến Môi trường (Lấy từ SSM) ---
  environment:
    # Lấy bí mật từ AWS SSM Parameter Store (Đã được mã hóa)
    DATABASE_URL: ${ssm:/my-app/${self:provider.stage}/database_url}
    JWT_SECRET: ${ssm:/my-app/${self:provider.stage}/jwt_secret}
    FRONTEND_URL: ${ssm:/my-app/${self:provider.stage}/frontend_url, 'http://localhost:3000'} # Thêm fallback local
    PRISMA_CLI_BINARY_TARGETS: rhel-openssl-3.0.x # Quan trọng cho Prisma
  
  # --- Cấp quyền IAM (Bắt buộc) ---
  # Thêm các quyền cần thiết để Lambda đọc SSM và truy cập các dịch vụ khác
  iam:
    role:
      statements:
        - Effect: 'Allow'
          Action:
            - 'ssm:GetParameter'
          Resource: 'arn:aws:ssm:${self:provider.region}:*:parameter/my-app/${self:provider.stage}/*'

  # --- Bảo vệ chống DDoS/Spam tiền (Giữ nguyên) ---
  apiGateway:
    usagePlan:
      quota:
        limit: 5000000
        period: MONTH
      throttle:
        burstLimit: 200
        rateLimit: 100

# --- Tối ưu Dung lượng Gói (Deployment Package) ---
build:
  esbuild:
    bundle: true
    minify: true
    sourcemap: false
    # Chỉ định rõ những module cần ngoại trừ khỏi gói chính
    external:
      - 'aws-sdk'
      - '@prisma/client/runtime/library'

package:
  individually: true
  patterns:
    - 'src/handler.js'
    - 'src/app.js'
    - 'src/**/*.js'
    - 'dist/**/*.js' # Đảm bảo file JS đã biên dịch được đóng gói
    - 'package.json'
    - 'node_modules/**'
    # --- Định nghĩa File Binary Prisma (Cực kỳ quan trọng) ---
    # Chỉ bao gồm file binary Linux cần thiết để gói < 250MB
    - 'node_modules/.prisma/client/libquery_engine-rhel-openssl-3.0.x.so.node'
    - 'node_modules/.prisma/client/schema.prisma'
    - '!./**' # Xóa tất cả rác sau khi đã định nghĩa các patterns cần thiết ở trên
    - '!node_modules/aws-sdk/**' # Giảm dung lượng bằng cách loại trừ SDK đã có sẵn trong Lambda

plugins:
  - serverless-offline
  - serverless-dotenv-plugin 

functions:
  api:
    handler: src/handler.handler
    events:
      - http: { path: /, method: ANY }
      - http: { path: /{proxy+}, method: ANY }
```

### 2. Các Bước Cần Thực hiện

#### A. Lưu Bí mật vào AWS SSM (Bảo mật)

Trước khi triển khai, bạn phải lưu các giá trị nhạy cảm vào AWS SSM Parameter Store để Serverless Framework có thể đọc chúng.

```bash
# Lệnh lưu DATABASE_URL (SecureString)
aws ssm put-parameter \
  --name "/my-app/dev/database_url" \
  --value "postgresql://user:password@endpoint..." \
  --type "SecureString" \
  --overwrite

# Lặp lại với JWT_SECRET và FRONTEND_URL
```

#### B. Chạy Thử nghiệm Cục bộ

Sử dụng plugin `serverless-offline` để chạy API cục bộ, kết nối trực tiếp đến NeonDB thông qua file `.env`.

```bash
# Chạy API cục bộ trên cổng 3000 (mặc định)
sls offline start
```

#### C. Triển khai Lần đầu

Sau khi code đã được kiểm thử cục bộ, bạn sẵn sàng triển khai toàn bộ hạ tầng lên AWS.

```bash
# Triển khai tất cả tài nguyên (Lambda, API Gateway, IAM)
sls deploy 
```