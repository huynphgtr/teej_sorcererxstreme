---
title : "Development workflow"
weight : 8
chapter : false
pre : " <b> 2.3.8 </b> "
---

The workflow is designed to leverage the speed of local development and the safety and automation of the Serverless architecture.

| Step | Activity | Environment | Role and Notes |
| :---: | :--- | :--- | :--- |
| **1. Code Development** | Code logic, API modifications (TypeScript/Express). | Local Machine | Use VS Code and Node.js libraries. |
| **2. Local Testing** | Test synchronous APIs. | `sls offline start` | Simulates the Lambda/API Gateway environment. Connects directly to NeonDB via the local `.env` file. |
| **3. Database Schema Update** | If Schema is modified (`schema.prisma`). | `npx prisma migrate dev` | Creates Migration and applies changes. |
| **4. Commit & Push Code** | Commit code and push to the repository. | `git push origin main` | Triggers the automated CI/CD flow. |
| **5. Automated CI/CD** | Deployment to the Cloud. | GitHub Actions | Automated: Build -> Prisma Generate -> AWS/SSM Login (fetch Keys) -> Deploy to AWS Lambda. |
| **6. Error Monitoring** | Check Real-time Logs. | `sls logs -f api -t` | Use the Serverless Framework command to immediately view CloudWatch Logs and debug errors in the Production/Staging environment. |

### Important Notes on Prisma:

1.  **Use `migrate dev`:** Instead of `db push` (which is only for non-production/test), you should use `npx prisma migrate dev` to create a history of changes (migration files) and apply them to NeonDB.
2.  **Generate on CI/CD:** Running `npx prisma generate` in GitHub Actions is mandatory to download the necessary binaries (`rhel-openssl-3.0.x`) for the AWS Lambda Linux environment.