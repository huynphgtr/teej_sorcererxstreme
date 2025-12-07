---
title: "Building Modern User Interface"
weight: 2
chapter: false
pre: " <b> 2.2.2. </b> "
---

### 1. Layout Design (Responsive & Glassmorphism)

Our goal is to create a mystical, "AI Sorcerer" interface like the Mockup below:

<img src="/images/layout_design_final.png" alt="Mystical UI Mockup" width="100%" />

Use **CSS Grid** and **Flexbox** from Tailwind to create flexible layouts.

```tsx
// src/app/layout.tsx
export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body className="bg-background text-white min-h-screen bg-[url('/bg-stars.png')] bg-cover">
        <div className="absolute inset-0 bg-black/50" /> {/* Overlay */}
        <main className="relative z-10 container mx-auto px-4 py-8 grid grid-cols-1 lg:grid-cols-12 gap-6">
          {/* Sidebar takes 3 cols on Desktop */}
          <aside className="lg:col-span-3 hidden lg:block">
            {/* Sidebar Content */}
          </aside>
          
          {/* Main Content takes 9 cols */}
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

Create a Widget displaying daily horoscope with Glassmorphism effect.

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

Build chat interface with auto-scroll and dynamic message styling.

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

### 4. Animation with Framer Motion

Add smooth entrance effects for UI elements.

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

### My Experience

{{% notice note %}}
**Mobile-First or Desktop-First?**
Initially, I designed for Desktop first, and when I opened it on mobile, the layout was broken.
**Lesson:** Always use classes like `hidden lg:block` or `grid-cols-1 lg:grid-cols-12` to prioritize Mobile layout first (default), then override for larger screens. TailwindCSS makes this incredibly easy!
{{% /notice %}}

### Verification & Testing

**Test Case 1: Responsive Design**
1.  Open browser on Desktop: See Sidebar on left, Chatbox on right.
2.  Press F12, switch to Mobile mode (iPhone 12/14).
3.  *Expected Result:* Sidebar hidden, Chatbox takes full width.

**Test Case 2: Hover Effects**
1.  Hover over `HoroscopeWidget`.
2.  *Expected Result:* Background brightens (`bg-white/20`), star icon scales up slightly (`scale-110`).
