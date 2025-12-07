---
title : "Setup environment"
weight : 2
chapter : false
pre : " <b> 2.3.2 </b> "
---

### Step 1: Project Initialization & Library Installation

Create the project directory and install all the necessary core dependencies.

```bash
# Create the main directory for the Backend
mkdir my-serverless-backend && cd my-serverless-backend

# Initialize a Node.js package
npm init -y

# Install Core Dependencies: Express, Prisma Client, Serverless-HTTP, etc.
npm install express cors dotenv @prisma/client axios serverless-http

# Install Development Dependencies: TypeScript, Types for Node/Express, Prisma CLI, Serverless-Offline
npm install -D typescript @types/node @types/express serverless-offline prisma serverless-dotenv-plugin
```

### Step 2: TypeScript & Directory Structure Setup

Configure TypeScript and create the standard directory structure.

1.  **Initialize `tsconfig.json`:**
    ```bash
    npx tsc --init
    ```
2.  **Edit `tsconfig.json`:** Open the `tsconfig.json` file and adjust the following settings to ensure modern Node.js source code and compatibility with Lambda:
      * `"target": "ES2020"` (or newer)
      * `"module": "commonjs"`
      * `"outDir": "./dist"` (Output directory for compiled code)
      * `"rootDir": "./src"` (Source code directory)
      * `"esModuleInterop": true`
      * `"strict": true`
3.  **Create Directory Structure:**
    ```bash
    mkdir src src/routes src/services src/controllers
    ```

### Step 3: Code Restructuring (Express -> Lambda)

Since Lambda does not "listen" on a standard port, we use `serverless-http` to wrap Express.

  * **File `src/app.ts` (Core Express App):**

    ```typescript
    import express from 'express';
    import cors from 'cors';
    import routes from './routes/index'; // Change to index if you use routes/index.ts

    const app = express();

    // 1. Basic Middlewares
    app.use(cors({ origin: process.env.FRONTEND_URL || '*' }));
    app.use(express.json());

    // 2. API Routing (Main Endpoint will be /api/...)
    app.use('/api', routes);

    // IMPORTANT: Do not use app.listen(), eliminate traditional web server logic.
    export default app;
    ```

  * **File `src/handler.ts` (Lambda Bridge):**

    ```typescript
    import serverless from "serverless-http";
    import app from "./app";

    // Export the main handler that AWS Lambda will call
    export const handler = serverless(app);
    ```

### Step 4: Configure Prisma & NeonDB Connection

To connect to **NeonDB** and ensure the **Prisma Client** works in the AWS Lambda Linux environment, the `binaryTargets` must be configured.

1.  **Create Schema File & Configure `binaryTargets`:**
    ```bash
    npx prisma init
    ```
    *Open the file `prisma/schema.prisma`* and add the configuration:
    ```prisma
    generator client {
      provider          = "prisma-client-js"
      // native: For the dev machine (Mac/Win)
      // rhel-openssl-3.0.x: For AWS Lambda (Node 20)
      binaryTargets     = ["native", "rhel-openssl-3.0.x"] 
    }

    datasource db {
      provider = "postgresql"
      url      = env("DATABASE_URL")
    }

    // ... model definitions (User, Partner, Reminder, etc.)
    ```
2.  **Create Local `.env` file:** Create the `.env` file and paste your **NeonDB** connection string into it.
    ```env
    # Get the PostgreSQL connection string from the Neon Console
    DATABASE_URL="postgresql://[user]:[password]@[endpoint]/[dbname]?sslmode=require"
    ```
3.  **Generate Prisma Client:** Run the following command to create the Prisma Client and download the necessary binaries.
    ```bash
    npx prisma generate
    ```

**Complete:** Your local environment is now ready. You can compile the code (`npm run build`) and run local tests with `serverless-offline`.