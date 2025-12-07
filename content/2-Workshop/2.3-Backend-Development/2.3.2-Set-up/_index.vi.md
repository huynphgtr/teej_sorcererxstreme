---
title : "Thiết lập môi trường"
weight : 2
chapter : false
pre : " <b> 2.3.2 </b> "
---

### Bước 1: Khởi tạo Project & Cài đặt Thư viện

Tạo thư mục dự án và cài đặt tất cả các dependencies cốt lõi cần thiết.

```bash
# Tạo thư mục chính cho Backend
mkdir my-serverless-backend && cd my-serverless-backend

# Khởi tạo gói Node.js
npm init -y

# Cài đặt Dependencies chính: Express, Prisma Client, Serverless-HTTP, v.v.
npm install express cors dotenv @prisma/client axios serverless-http

# Cài đặt Dependencies Phát triển: TypeScript, Types cho Node/Express, Prisma CLI, Serverless-Offline
npm install -D typescript @types/node @types/express serverless-offline prisma serverless-dotenv-plugin
```

### Bước 2: Thiết lập TypeScript & Cấu trúc Thư mục

Cấu hình TypeScript và tạo cấu trúc thư mục tiêu chuẩn.

1.  **Khởi tạo `tsconfig.json`:**
    ```bash
    npx tsc --init
    ```
2.  **Chỉnh sửa `tsconfig.json`:** Mở file `tsconfig.json` và điều chỉnh các thiết lập sau để đảm bảo mã nguồn Node.js hiện đại và tương thích với Lambda:
      * `"target": "ES2020"` (Hoặc mới hơn)
      * `"module": "commonjs"`
      * `"outDir": "./dist"` (Đầu ra của mã đã biên dịch)
      * `"rootDir": "./src"` (Thư mục chứa mã nguồn)
      * `"esModuleInterop": true`
      * `"strict": true`
3.  **Tạo Cấu trúc Thư mục:**
    ```bash
    mkdir src src/routes src/services src/controllers
    ```

### Bước 3: Cấu trúc lại Code (Express -> Lambda)

Vì Lambda không "lắng nghe" cổng thông thường, chúng ta dùng `serverless-http` để đóng gói Express.

  * **File `src/app.ts` (Core Express App):**

    ```typescript
    import express from 'express';
    import cors from 'cors';
    import routes from './routes/index'; // Thay đổi thành index nếu bạn dùng routes/index.ts

    const app = express();

    // 1. Middlewares cơ bản
    app.use(cors({ origin: process.env.FRONTEND_URL || '*' }));
    app.use(express.json());

    // 2. Định tuyến API (Endpoint chính sẽ là /api/...)
    app.use('/api', routes);

    // QUAN TRỌNG: Không dùng app.listen(), loại bỏ logic web server truyền thống.
    export default app;
    ```

  * **File `src/handler.ts` (Cầu nối Lambda):**

    ```typescript
    import serverless from "serverless-http";
    import app from "./app";

    // Xuất handler chính mà AWS Lambda sẽ gọi
    export const handler = serverless(app);
    ```

### Bước 4: Cấu hình Prisma & Kết nối NeonDB 

Để kết nối với **NeonDB** và đảm bảo **Prisma Client** hoạt động trên môi trường Linux của AWS Lambda, cần cấu hình `binaryTargets`.

1.  **Tạo File Schema & Cấu hình `binaryTargets`:**
    ```bash
    npx prisma init
    ```
    *Mở file `prisma/schema.prisma`* và thêm cấu hình:
    ```prisma
    generator client {
      provider        = "prisma-client-js"
      // native: Cho máy dev (Mac/Win)
      // rhel-openssl-3.0.x: Cho AWS Lambda (Node 20)
      binaryTargets   = ["native", "rhel-openssl-3.0.x"] 
    }

    datasource db {
      provider = "postgresql"
      url      = env("DATABASE_URL")
    }

    // ... model definitions (User, Partner, Reminder, v.v.)
    ```
2.  **Tạo file `.env` Local:** Tạo file `.env` và dán chuỗi kết nối **NeonDB** của bạn vào.
    ```env
    # Lấy chuỗi kết nối PostgreSQL từ Neon Console
    DATABASE_URL="postgresql://[user]:[password]@[endpoint]/[dbname]?sslmode=require"
    ```
3.  **Generate Prisma Client:** Chạy lệnh sau để tạo Prisma Client và tải xuống các binary cần thiết.
    ```bash
    npx prisma generate
    ```

**Hoàn tất:** Giờ đây, môi trường local của bạn đã sẵn sàng. Bạn có thể biên dịch code (`npm run build`) và chạy thử nghiệm cục bộ với `serverless-offline`.