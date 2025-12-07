---
title: "Setting up CI/CD Pipeline with AWS Amplify"
weight: 4
chapter: false
pre: " <b> 2.2.4. </b> "
---

### 1. CI/CD Process

We will set up a fully automated process as follows:

![CI/CD Pipeline Diagram](/images/cicd_pipeline_diagram_1764662962948.png)

*   **Source:** Developer pushes code to GitHub.
*   **Build:** Amplify automatically detects changes, installs dependencies, and builds Next.js.
*   **Deploy:** Pushes built code to global Hosting system.
*   **Verify:** Checks website health (Health Check).

### 2. Connect Repository

1.  Push code to Git Repository (GitHub/GitLab/CodeCommit).
2.  In AWS Amplify Console, select **Host web app**.
3.  Connect to the Repository containing Frontend source code.

### 3. Build Configuration (Build Specification)

Amplify uses `amplify.yml` to define build steps.

```yaml
version: 1
frontend:
  phases:
    preBuild:
      commands:
        - npm ci
    build:
      commands:
        - npm run build
  artifacts:
    baseDirectory: .next
    files:
      - '**/*'
  cache:
    paths:
      - node_modules/**/*
```

### 4. Environment Variables Management

Never hard-code sensitive or environment-specific values (like API URLs) in your code. Use Environment Variables in Amplify.

1.  Go to **App settings** > **Environment variables**.
2.  Add variable:
    *   Key: `NEXT_PUBLIC_API_URL`
    *   Value: `https://xyz.execute-api.us-east-1.amazonaws.com/prod`
3.  Access in Next.js code via `process.env.NEXT_PUBLIC_API_URL`.

*Note: Variables starting with `NEXT_PUBLIC_` will be embedded into the Frontend code by Next.js at build time.*

### 5. Branch Previews (Pull Request Previews)

This feature is incredibly useful for team collaboration.
*   When you create a Pull Request (PR) on GitHub, Amplify automatically creates a temporary Preview environment (with a unique URL).
*   Team Leaders can visit that URL to review new features before Merging into the main branch.
*   After Merging, the Preview environment is automatically deleted.

To enable: Go to **App settings** > **Previews** > **Enable previews**.

---

### My Experience

{{% notice note %}}
**Build Error on Linux vs Windows**
My machine uses Windows (case-insensitive), but Amplify runs Linux (case-sensitive). Once I imported `Component.tsx` but the file was named `component.tsx`. It ran fine locally, but failed on Amplify with "File not found".
**Lesson:** Always name files consistently (PascalCase for Components, camelCase for utils) and double-check when renaming files.
{{% /notice %}}

### Verification & Testing

**Test Case 1: Trigger Build**
1.  Edit a small text line in `page.tsx`.
2.  Commit and Push to GitHub: `git push origin main`.
3.  Go to Amplify Console.
4.  *Expected Result:* See status change to "Provisioning" -> "Building" -> "Deploying".

**Test Case 2: Check Logs**
1.  Click on the running build.
2.  Open **Frontend** tab.
3.  *Expected Result:* See green logs (Success) in steps.
    ```
    2024-05-20T10:00:00.000Z [INFO]: # Executing command: npm run build
    ...
    2024-05-20T10:01:00.000Z [INFO]: Compiled successfully
    ```
