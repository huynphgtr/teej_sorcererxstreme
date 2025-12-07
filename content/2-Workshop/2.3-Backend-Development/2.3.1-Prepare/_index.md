---
title : "Preparation"
weight : 1
chapter : false
pre : " <b> 2.3.1 </b> "
---

### 1. Technologies Used

| Category | Technology | Detail & Role |
| :--- | :--- | :--- |
| **Language** | TypeScript (Node.js 20) | Primary language, providing Type Safety. |
| **Backend Core** | Express.js + `serverless-http` | Familiar API Framework, "wrapped" to run on Lambda. |
| **Infrastructure (IaC)** | Serverless Framework V4 | Main tool for defining and deploying the entire AWS architecture. |
| **Compute** | AWS Lambda | Handles business logic and runs the TypeScript code. |
| **API Gateway** | AWS API Gateway | Synchronous HTTP communication gateway for the entire Backend. |
| **Database** | NeonDB (Serverless PostgreSQL) | Primary database for relational data. |
| **ORM** | Prisma | Abstraction Layer between the code and the database. |
| **Security** | AWS SSM Parameter Store | Secure storage for sensitive environment variables. |
| **DevOps** | GitHub Actions | Automates the CI/CD pipeline. |

### 2. Required Resources & Software

To complete the workshop, users must have the following tools and accounts ready on their computer.

#### A. Account Requirements

  * **AWS Account:** Necessary for deploying Serverless services (Lambda, API Gateway, SSM).
  * **NeonDB Account:** Necessary for creating and retrieving the connection string (`DATABASE_URL`) for the PostgreSQL database.
  * **GitHub Account:** Necessary for storing the source code and setting up CI/CD (GitHub Actions).

#### B. Local Software & Tools

  * **Node.js (v20+):** Installed and accessible via the terminal.
  * **npm or yarn:** Package manager.
  * **AWS CLI:** Necessary to configure AWS access permissions from the local machine for the Serverless Framework.
  * **IDE (VS Code):** Recommended development environment.
  * **Postman/Insomnia:** Essential tool for testing API Endpoints (GET/POST).

#### C. Project Setup

  * **Serverless Framework CLI:** Must be installed globally (`npm install -g serverless`).
  * **AWS Credentials:** Configure AWS access permissions (User/Role) on the local machine.