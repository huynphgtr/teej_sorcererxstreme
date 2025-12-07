---
title: "Xây dựng giao diện người dùng hiện đại"
weight: 2
chapter: false
pre: " <b> 2.2.2. </b> "
---

### 1. Thiết kế Layout (Responsive & Glassmorphism)

Mục tiêu của chúng ta là tạo ra một giao diện huyền bí, đậm chất "Phù thủy AI" như bản Mockup dưới đây:

<img src="/images/layout_design_final.png" alt="Mystical UI Mockup" width="100%" />

Sử dụng **CSS Grid** và **Flexbox** của Tailwind để tạo layout linh hoạt.

```tsx
// src/app/layout.tsx
export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body className="bg-background text-white min-h-screen bg-[url('/bg-stars.png')] bg-cover">
        <div className="absolute inset-0 bg-black/50" /> {/* Overlay */}
        <main className="relative z-10 container mx-auto px-4 py-8 grid grid-cols-1 lg:grid-cols-12 gap-6">
          {/* Sidebar chiếm 3 cột trên Desktop */}
          <aside className="lg:col-span-3 hidden lg:block">
            {/* Sidebar Content */}
          </aside>
          
          {/* Main Content chiếm 9 cột */}
          <section className="lg:col-span-9">
            {children}
          </section>
        </main>
      </body>
    </html>
  )
}
```

### 2. Component: Horoscope Widget

Tạo một Widget hiển thị thông tin tử vi hàng ngày với hiệu ứng Glassmorphism.

```tsx
// src/components/HoroscopeWidget.tsx
import { Star } from 'lucide-react';

export default function HoroscopeWidget({ sign, prediction }: { sign: string, prediction: string }) {
  return (
    <div className="p-6 rounded-2xl bg-white/10 backdrop-blur-lg border border-white/20 hover:bg-white/20 transition-all duration-300 group">
      <div className="flex items-center gap-3 mb-4">
        <div className="p-3 rounded-full bg-accent/20 text-accent group-hover:scale-110 transition-transform">
          <Star size={24} />
        </div>
        <h3 className="text-xl font-bold">{sign}</h3>
      </div>
      <p className="text-gray-300 leading-relaxed">{prediction}</p>
    </div>
  );
}
```

### 3. Component: Chat Interface

Xây dựng giao diện chat với khả năng tự động cuộn và styling tin nhắn động.

```tsx
// src/components/ChatBox.tsx
import { useEffect, useRef } from 'react';
import { clsx } from 'clsx';

export default function ChatBox({ messages }: { messages: Message[] }) {
  const bottomRef = useRef<HTMLDivElement>(null);

  // Auto-scroll to bottom
  useEffect(() => {
    bottomRef.current?.scrollIntoView({ behavior: 'smooth' });
  }, [messages]);

  return (
    <div className="flex flex-col h-[600px] bg-white/5 rounded-xl border border-white/10 overflow-hidden">
      <div className="flex-1 overflow-y-auto p-4 space-y-4">
        {messages.map((msg, idx) => (
          <div key={idx} className={clsx(
            "max-w-[80%] p-3 rounded-lg",
            msg.role === 'user' 
              ? "bg-primary self-end ml-auto" 
              : "bg-white/10 self-start mr-auto"
          )}>
            {msg.content}
          </div>
        ))}
        <div ref={bottomRef} />
      </div>
      {/* Input Area */}
    </div>
  );
}
```

### 4. Animation với Framer Motion

Thêm hiệu ứng xuất hiện mượt mà cho các phần tử UI.

```tsx
import { motion } from 'framer-motion';

const fadeIn = {
  hidden: { opacity: 0, y: 20 },
  visible: { opacity: 1, y: 0 }
};

<motion.div 
  initial="hidden"
  animate="visible"
  variants={fadeIn}
  transition={{ duration: 0.5 }}
>
  <HoroscopeWidget sign="Leo" prediction="Today is your lucky day!" />
</motion.div>
```

---

### Chuyện nghề (My Experience)

{{% notice note %}}
**Mobile-First hay Desktop-First?**
Ban đầu mình thiết kế trên Desktop trước, đến khi mở trên điện thoại thì vỡ layout tùm lum.
**Bài học:** Luôn dùng class `hidden lg:block` hoặc `grid-cols-1 lg:grid-cols-12` để ưu tiên giao diện Mobile trước (mặc định), sau đó mới override cho màn hình lớn. TailwindCSS được sinh ra để làm việc này cực dễ!
{{% /notice %}}

### Kiểm thử & Xác thực (Verification)

**Test Case 1: Responsive Design**
1.  Mở trình duyệt trên Desktop: Thấy Sidebar bên trái, Chatbox bên phải.
2.  Nhấn F12, chuyển sang chế độ Mobile (iPhone 12/14).
3.  *Kết quả mong đợi:* Sidebar ẩn đi, Chatbox tràn màn hình (full width).

**Test Case 2: Hiệu ứng Hover**
1.  Di chuột vào `HoroscopeWidget`.
2.  *Kết quả mong đợi:* Nền sáng lên (`bg-white/20`), icon ngôi sao phóng to nhẹ (`scale-110`).
