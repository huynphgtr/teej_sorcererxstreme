---
title: "Tích hợp API Gateway & Authentication"
weight: 3
chapter: false
pre: " <b> 2.2.3. </b> "
---

### 1. Luồng xác thực (Authentication Flow)

Trước khi đi vào code, hãy hiểu cách Frontend giao tiếp với Cognito và API Gateway:

![Auth Flow Diagram](/images/auth_flow_diagram_1764662901162.png)

1.  User nhập User/Pass.
2.  Cognito trả về **JWT Token** (ID Token, Access Token).
3.  Frontend gửi Request kèm Token trong Header `Authorization`.
4.  API Gateway xác thực Token. Nếu hợp lệ -> Chuyển tiếp cho Lambda.

### 2. Cấu hình AWS Amplify

Cài đặt thư viện `aws-amplify` để kết nối Frontend với các dịch vụ AWS:

```bash
npm install aws-amplify @aws-amplify/ui-react
```

Cấu hình trong `src/app/layout.tsx`:

```tsx
'use client';
import { Amplify } from 'aws-amplify';
import config from '@/amplifyconfiguration.json';

Amplify.configure(config);
```

### 3. Tích hợp Amazon Cognito (Authentication)

Sử dụng `Authenticator` component để tạo luồng đăng nhập/đăng ký bảo mật:

```tsx
import { Authenticator } from '@aws-amplify/ui-react';

export default function LoginPage() {
  return (
    <Authenticator>
      {({ signOut, user }) => (
        <main>
          <h1>Xin chào, {user?.username}</h1>
          <button onClick={signOut}>Đăng xuất</button>
        </main>
      )}
    </Authenticator>
  );
}
```

### 4. Custom Hook: `useAuth` (Best Practices)

Thay vì gọi trực tiếp `fetchAuthSession` ở khắp nơi, hãy tạo một Custom Hook để tái sử dụng logic xác thực và lấy Token.

```typescript
// src/hooks/useAuth.ts
import { fetchAuthSession } from 'aws-amplify/auth';
import { useState, useEffect } from 'react';

export function useAuth() {
  const [token, setToken] = useState<string | null>(null);

  useEffect(() => {
    const getToken = async () => {
      try {
        const session = await fetchAuthSession();
        setToken(session.tokens?.idToken?.toString() || null);
      } catch (err) {
        console.error("Error fetching auth session", err);
      }
    };
    getToken();
  }, []);

  return { token };
}
```

### 5. Gọi API với Error Handling

Xử lý lỗi chuyên nghiệp bằng `try/catch/finally` và hiển thị thông báo cho người dùng.

```typescript
// src/services/api.ts
export const chatWithAI = async (message: string, token: string) => {
  try {
    const response = await fetch(`${process.env.NEXT_PUBLIC_API_URL}/chat`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${token}`
      },
      body: JSON.stringify({ message })
    });

    if (!response.ok) {
      if (response.status === 401) throw new Error("Phiên đăng nhập hết hạn");
      if (response.status === 429) throw new Error("Bạn gửi tin nhắn quá nhanh");
      throw new Error("Lỗi hệ thống");
    }

    return await response.json();
  } catch (error) {
    console.error("API Error:", error);
    throw error; // Ném lỗi ra để UI xử lý hiển thị
  }
};
```

---

### Chuyện nghề (My Experience)

{{% notice warning %}}
**Đừng bao giờ lộ API Key!**
Có lần mình lỡ commit file `.env` chứa API Key lên GitHub. Hậu quả là AWS gửi mail cảnh báo ngay lập tức.
**Giải pháp:** Luôn thêm `.env` vào `.gitignore`. Với Amplify, file `amplifyconfiguration.json` an toàn để public vì nó chỉ chứa ID của các resource (như User Pool ID), không chứa Secret Key.
{{% /notice %}}

### Kiểm thử & Xác thực (Verification)

**Test Case 1: Đăng nhập thành công**
1.  Vào trang Login, nhập User/Pass đã tạo.
2.  Nhấn Sign In.
3.  *Kết quả mong đợi:* Chuyển hướng vào trang chính, hiển thị "Xin chào, [Username]".

**Test Case 2: Kiểm tra Token**
1.  Mở DevTools (F12) -> Network Tab.
2.  Gửi một tin nhắn Chat.
3.  Tìm request gửi đến API Gateway.
4.  Kiểm tra Header: Phải có dòng `Authorization: Bearer eyJra...` (JWT Token).
