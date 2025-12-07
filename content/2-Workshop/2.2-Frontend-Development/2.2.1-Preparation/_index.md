---
title: "Setting up Development Environment"
weight: 1
chapter: false
pre: " <b> 2.2.1. </b> "
---

### 1. Prerequisites

To develop modern web applications, you need the following standard tools:

*   **Node.js (LTS Version):** Runtime environment for JavaScript/TypeScript.
*   **Git:** Distributed version control system.
*   **IDE:** Visual Studio Code (recommended).

**Recommended VS Code Extensions:**
*   **ESLint & Prettier:** Automate formatting and linting.
*   **Tailwind CSS IntelliSense:** Rapid Tailwind class suggestions.
*   **ES7+ React/Redux/React-Native snippets:** Code faster with shortcuts.

### 2. Initialize Next.js Project

We will use **Next.js** - the most popular React Framework today.

Run initialization command:

```bash
npx create-next-app@latest my-serverless-app
```

Detailed Configuration:
*   TypeScript: **Yes** (Type safety)
*   Tailwind CSS: **Yes** (Rapid styling)
*   ESLint: **Yes** (Linting)
*   App Router: **Yes** (Latest routing architecture)
*   Import Alias: **@/** (Cleaner imports)

### 3. Initialize AWS Amplify (Backend)

This is a crucial step to integrate Serverless features (Auth, Data) into your project.
Run the following command inside your project folder:

```bash
cd my-serverless-app
npm create amplify@latest
```

When prompt to install, select **Yes**. Amplify will automatically create the `amplify/` folder containing the Backend structure.

### 4. Install AWS Libraries

Install necessary SDK packages for Frontend to communicate with AWS:

```bash
npm install aws-amplify @aws-amplify/ui-react
```

### 5. Standard Project Structure

After installation, your folder structure should look like this:

![VS Code Project Structure](/images/vscode_project_structure.png)

*   `amplify/`: Contains Backend code (auth.ts, data.ts).
*   `src/app`: Contains Pages and Layouts (App Router).
*   `src/components`: Contains reusable UI Components.
*   `amplify_outputs.json`: Auto-generated config file (Do not edit).

### 6. Configure `tsconfig.json` (Best Practices)

To ensure strict TypeScript coding, update `tsconfig.json`:

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

### 7. Install UI Libraries

To create the "Mystical" UI:

```bash
npm install framer-motion lucide-react clsx tailwind-merge
```

### 8. Configure TailwindCSS

Set up colors in `tailwind.config.ts`:

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

### My Experience

{{% notice tip %}}
**Amplify Gen 2 vs Gen 1:**
If you used Amplify CLI (Gen 1) before with commands like `amplify add auth`, forget it!
Gen 2 (what we are using) is **Code-First**. You define Backend using TypeScript (in `amplify/` folder) instead of clicking through Console. It gives Frontend Devs much better control over infrastructure.
{{% /notice %}}

### Verification & Testing

**Test Case: Check Amplify Installation**
1.  Open `package.json`.
2.  Look inside `dependencies`.
3.  *Expected Result:* You should see `"aws-amplify": "^6.x.x"` and `"@aws-amplify/backend": "^1.x.x"`.
