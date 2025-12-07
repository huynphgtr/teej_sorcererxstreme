---
title: "Optimization & Advanced Deployment"
weight: 5
chapter: false
pre: " <b> 2.2.5. </b> "
---


After successfully deploying **SorcererXtreme** to the Internet, our work is not done. To make the product truly "Production-Ready", we need to perform advanced tuning.

### 1. Custom Domain

By default, AWS Amplify provides a long and hard-to-remember URL (e.g., `main.d12345.amplifyapp.com`). Setting up a custom domain not only makes the app look professional but also improves trust.

**Process:**

1.  **Purchase Domain:** You can buy directly on **Amazon Route 53** or other providers (Namecheap, GoDaddy).
2.  **Configure in Amplify:**
    *   Go to **App settings** > **Domain management**.
    *   Click **Add domain** and enter your domain (e.g., `sorcererxtreme.vn`).
3.  **DNS Verification:**
    *   If on Route 53: Amplify auto-configures (Zero-config).
    *   If external: Amplify provides a CNAME record for you to add to your provider's DNS manager.
4.  **SSL/TLS:** Amplify automatically issues and manages free SSL certificates, ensuring the green padlock (HTTPS).

### 2. Environment Variables Management

During development, we often use `.env.local` files to store sensitive Keys. However, these files are not pushed to Git when deploying.

**Why important?**
*   **Security:** Hides API keys (like API Gateway Endpoint, Cognito User Pool ID) from public source code.
*   **Flexibility:** Easily switch configs between Staging and Production environments without changing code.

**Setup:**
1.  Access Amplify Console > Select App > **Environment variables**.
2.  Enter corresponding Keys (e.g., `NEXT_PUBLIC_API_URL`, `NEXT_PUBLIC_USER_POOL_ID`).
3.  Trigger the Build process again for changes to take effect.

### 3. SEO Optimization for Next.js (Search Engine Optimization)

For a B2C user-facing app like Tarot reading, appearing on Google is vital. Next.js (App Router) strongly supports SEO via the **Metadata API**.

**Dynamic Metadata:**
Instead of static titles, create dynamic titles based on the Tarot card the user draws:

```tsx
// app/tarot/[cardId]/page.tsx

export async function generateMetadata({ params }) {
  const card = await getTarotCard(params.cardId);
  return {
    title: `Meaning of ${card.name} | SorcererXtreme`,
    description: `Discover the cosmic message from ${card.name}...`,
    openGraph: {
      images: [card.imageUrl], // Image shown when shared on Facebook
    },
  }
}
```

### 4. Monitoring & Analytics

You cannot improve what you do not measure. Use the **Monitoring** Tab in Amplify to track:

*   **Incoming Traffic:** Number of visitors in real-time.
*   **Data Transfer:** Bandwidth usage.
*   **Error Rate (4XX/5XX):** Detect immediately if APIs fail or links break.
*   **Access Logs:** Download access logs to analyze user behavior (origin country, browser used).

---
{{% notice tip %}}
**Front-end Tip:** Always check your **Lighthouse** score (in Chrome DevTools) after every deploy. A beautiful app that loads slowly will lose users instantly. Optimize images (use WebP/AVIF format) and lazy-load heavy components.
{{% /notice %}}
