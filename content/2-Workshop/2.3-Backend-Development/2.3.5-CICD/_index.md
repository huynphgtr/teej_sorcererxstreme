---
title : "Automation CI/CD"
weight : 5
chapter : false
pre : " <b> 2.3.5 </b> "
---

The objective of this section is to establish a process so that every time code is pushed to the `main` branch, your entire Serverless architecture will be automatically **Built, Tested, and Deployed** to AWS without manual intervention from a local machine.

### 1. Preparing Secrets on the GitHub Repository

This is the most crucial step for granting GitHub Actions access to AWS.

| Secret Name | Role | Notes and Action |
| :--- | :--- | :--- |
| `AWS_ACCESS_KEY_ID` | AWS Access Key. | **VERY IMPORTANT:** Use an **IAM User** with minimum necessary permissions (not Admin) to only allow creation/updating of Lambda, API Gateway resources, and reading SSM. |
| `AWS_SECRET_ACCESS_KEY` | Corresponding Secret Key. | (Keep as is) |
| `SERVERLESS_ACCESS_KEY` | Serverless Dashboard Key. | Only needed if you use Serverless Dashboard management features. If not, this can be omitted. |

### 2. Workflow File `.github/workflows/deploy.yml`

This file defines the steps that GitHub Actions will execute. We will add the **Build** step and omit environment variables unnecessary for the `deploy` command.

```yaml
name: Deploy Backend CI/CD via Serverless Framework

on:
  push:
    branches: [ main ] # Triggers when pushing to the main branch
  workflow_dispatch: # Allows manual triggering from the GitHub UI

jobs:
  deploy:
    runs-on: ubuntu-latest
    # Grant permissions for GitHub Actions
    permissions:
      id-token: write 
      contents: read
      
    steps:
      - uses: actions/checkout@v4 # 1. Get source code
      
      - name: Setup Node 20
        uses: actions/setup-node@v3
        with: 
          node-version: '20'
          cache: 'npm' # Enable cache to speed up installation

      - name: Install Dependencies
        run: npm ci # Use npm ci to ensure consistency (from package-lock.json)

      # --- PRISMA & CODE BUILD CONFIGURATION ---
      - name: Generate Prisma Client (download Linux binary)
        # Download the rhel-openssl-3.0.x binary (necessary for Lambda)
        run: npx prisma generate
        
      - name: Build TypeScript Code (tsc -> dist)
        # Run the compilation command (assumes a "build": "tsc" script in package.json)
        run: npm run build 

      # --- AWS CONFIGURATION ---
      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ap-southeast-1

      # --- FINAL DEPLOYMENT ---
      - name: Deploy Serverless
        # Use npx to call the serverless CLI
        run: npx serverless deploy
        env:
          # Explicitly specify the deployment stage. 
          SLS_STAGE: dev 
          # [Note]: The DATABASE_URL variable is already handled by placeholder or SSM
          DATABASE_URL: "placeholder" 
```

### 3. CI/CD Workflow Summary

1.  **Code Commit:** You develop code and perform a `git push` to GitHub.
2.  **Trigger:** GitHub Actions automatically triggers the `deploy.yml` workflow.
3.  **Build & Package:** The Ubuntu Runner installs dependencies, runs `npx prisma generate` (downloading the Linux binary), and compiles the code.
4.  **AWS Authentication:** `aws-actions/configure-aws-credentials` logs into AWS using your Secret Keys.
5.  **Provisioning:** The `npx serverless deploy` command reads `serverless.yml`, connects to **SSM Parameter Store** to fetch secret keys, and instructs **CloudFormation** to build/update the entire Lambda and API Gateway infrastructure.