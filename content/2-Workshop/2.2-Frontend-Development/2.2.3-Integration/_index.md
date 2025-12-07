---
title: "Integrating API Gateway & Authentication"
weight: 3
chapter: false
pre: " <b> 2.2.3. </b> "
---

### 1. Authentication Flow

Before coding, let's understand how Frontend communicates with Cognito and API Gateway:

![Auth Flow Diagram](/images/auth_flow_diagram_1764662901162.png)

1.  User enters User/Pass.
2.  Cognito returns **JWT Token** (ID Token, Access Token).
3.  Frontend sends Request with Token in `Authorization` Header.
4.  API Gateway verifies Token. If valid -> Forward to Lambda.

### 2. Configure AWS Amplify

Install `aws-amplify` library to connect Frontend with AWS services:

```bash
npm install aws-amplify @aws-amplify/ui-react
```

Configure in `src/app/layout.tsx`:

```tsx
'use client';
import { Amplify } from 'aws-amplify';
import config from '@/amplifyconfiguration.json';

Amplify.configure(config);
```

### 3. Integrate Amazon Cognito (Authentication)

Use `Authenticator` component to create a secure login/signup flow:

```tsx
import { Authenticator } from '@aws-amplify/ui-react';

export default function LoginPage() {
  return (
    <Authenticator>
      {({ signOut, user }) => (
        <main>
          <h1>Welcome, {user?.username}</h1>
          <button onClick={signOut}>Sign Out</button>
        </main>
      )}
    </Authenticator>
  );
}
```

### 4. Custom Hook: `useAuth` (Best Practices)

Instead of calling `fetchAuthSession` everywhere, create a Custom Hook to reuse authentication logic and token retrieval.

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

### 5. API Call with Error Handling

Handle errors professionally using `try/catch/finally` and display feedback to users.

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
      if (response.status === 401) throw new Error("Session expired");
      if (response.status === 429) throw new Error("Too many requests");
      throw new Error("System error");
    }

    return await response.json();
  } catch (error) {
    console.error("API Error:", error);
    throw error; // Throw error for UI to handle
  }
};
```

---

### My Experience

{{% notice warning %}}
**Never expose API Keys!**
Once I accidentally committed a `.env` file containing API Keys to GitHub. AWS sent a warning email immediately.
**Solution:** Always add `.env` to `.gitignore`. With Amplify, `amplifyconfiguration.json` is safe to be public as it only contains Resource IDs (like User Pool ID), not Secret Keys.
{{% /notice %}}

### Verification & Testing

**Test Case 1: Successful Login**
1.  Go to Login page, enter created User/Pass.
2.  Click Sign In.
3.  *Expected Result:* Redirect to main page, display "Welcome, [Username]".

**Test Case 2: Token Check**
1.  Open DevTools (F12) -> Network Tab.
2.  Send a Chat message.
3.  Find request sent to API Gateway.
4.  Check Header: Must have `Authorization: Bearer eyJra...` line (JWT Token).
