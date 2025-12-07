---
title: "Chuẩn bị môi trường phát triển"
weight: 1
chapter: false
pre: " <b> 2.2.1. </b> "
---

### 1. Cài đặt công cụ (Prerequisites)

Để phát triển ứng dụng web hiện đại, bạn cần chuẩn bị các công cụ tiêu chuẩn sau:

*   **Node.js (LTS Version):** Môi trường runtime cho JavaScript/TypeScript.
*   **Git:** Hệ thống quản lý phiên bản phân tán.
*   **IDE:** Visual Studio Code (khuyên dùng).

**VS Code Extensions khuyến nghị:**
*   **ESLint & Prettier:** Tự động format và kiểm tra lỗi code.
*   **Tailwind CSS IntelliSense:** Gợi ý class Tailwind cực nhanh.
*   **ES7+ React/Redux/React-Native snippets:** Code nhanh hơn với các phím tắt.

### 2. Khởi tạo dự án Next.js

Chúng ta sẽ sử dụng **Next.js** - React Framework phổ biến nhất hiện nay.

Chạy lệnh khởi tạo:

```bash
npx create-next-app@latest my-serverless-app
```

Cấu hình chi tiết:
*   TypeScript: **Yes** (Tăng tính chặt chẽ cho code)
*   Tailwind CSS: **Yes** (Styling nhanh chóng)
*   ESLint: **Yes** (Kiểm tra lỗi)
*   App Router: **Yes** (Kiến trúc routing mới nhất)
*   Import Alias: **@/** (Giúp import file gọn gàng hơn)

### 3. Khởi tạo AWS Amplify (Backend)

Đây là bước quan trọng để tích hợp các tính năng Serverless (Auth, Data) vào dự án.
Chạy lệnh sau trong thư mục dự án:

```bash
cd my-serverless-app
npm create amplify@latest
```

Khi được hỏi cài đặt, chọn **Yes**. Amplify sẽ tự động tạo thư mục `amplify/` chứa cấu trúc Backend.

### 4. Cài đặt thư viện AWS

Cài đặt các gói SDK cần thiết để Frontend giao tiếp với AWS:

```bash
npm install aws-amplify @aws-amplify/ui-react
```

### 5. Cấu trúc dự án chuẩn (Project Structure)

Sau khi cài đặt xong, cấu trúc thư mục sẽ trông như sau:

![VS Code Project Structure](/images/vscode_project_structure.png)

*   `amplify/`: Chứa code Backend (auth.ts, data.ts).
*   `src/app`: Chứa các Pages và Layout (App Router).
*   `src/components`: Chứa các UI Components tái sử dụng.
*   `amplify_outputs.json`: File cấu hình tự động sinh ra (Không sửa file này).

### 6. Cấu hình `tsconfig.json` (Best Practices)

Để đảm bảo code TypeScript chặt chẽ nhất, hãy cập nhật `tsconfig.json`:

```json
{
  "compilerOptions": {
    "target": "es5",
    "lib": ["dom", "dom.iterable", "esnext"],
    "allowJs": true,
    "skipLibCheck": true,
    "strict": true,
    "forceConsistentCasingInFileNames": true,
    "noEmit": true,
    "esModuleInterop": true,
    "module": "esnext",
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve",
    "incremental": true,
    "plugins": [
      {
        "name": "next"
      }
    ],
    "paths": {
      "@/*": ["./src/*"]
    }
  },
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx", ".next/types/**/*.ts"],
  "exclude": ["node_modules"]
}
```

### 7. Cài đặt thư viện UI bổ trợ

Để tạo giao diện "Mystical" đep mắt:

```bash
npm install framer-motion lucide-react clsx tailwind-merge
```

### 8. Cấu hình TailwindCSS

Thiết lập biến màu trong `tailwind.config.ts`:

```typescript
// tailwind.config.ts
import type { Config } from "tailwindcss";

const config: Config = {
  content: [
    "./src/pages/**/*.{js,ts,jsx,tsx,mdx}",
    "./src/components/**/*.{js,ts,jsx,tsx,mdx}",
    "./src/app/**/*.{js,ts,jsx,tsx,mdx}",
  ],
  theme: {
    extend: {
      colors: {
        primary: "#432c7a",
        secondary: "#764ba2",
        accent: "#ffd700",
        background: "#1a0b2e",
      },
      backgroundImage: {
        "gradient-radial": "radial-gradient(var(--tw-gradient-stops))",
      },
    },
  },
  plugins: [],
};
export default config;
```

---

### Chuyện nghề (My Experience)

{{% notice tip %}}
**Amplify Gen 2 vs Gen 1:**
Nếu bạn từng dùng Amplify CLI (Gen 1) với hàng tá câu lệnh `amplify add auth`, hãy quên nó đi!
Gen 2 (chúng ta đang dùng) là **Code-First**. Bạn định nghĩa Backend bằng TypeScript (trong folder `amplify/`) thay vì click chuột trên Console. Nó giúp Frontend Dev kiểm soát hạ tầng dễ dàng hơn nhiều.
{{% /notice %}}

### Kiểm thử & Xác thực (Verification)

**Test Case: Kiểm tra cài đặt Amplify**
1.  Mở file `package.json`.
2.  Tìm trong mục `dependencies`.
3.  *Kết quả mong đợi:* Phải thấy dòng `"aws-amplify": "^6.x.x"` và `"@aws-amplify/backend": "^1.x.x"`.
