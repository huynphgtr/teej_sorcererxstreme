---
title: "Workshop"
weight: 1
chapter: false
pre: " <b> 2. </b> "
---

# SorcererXtreme: Building an AI-Powered Interpretation Platform on AWS

### Overview

SorcererXtreme AI is a pioneering metaphysical guidance platform that leverages AI and AWS Serverless architecture to provide personalized, grounded, and reliable readings in Astrology, Tarot, Horoscopes, and Numerology.

### 1. Frontend Development

This section focuses on building the React/Next.js user interface and integrating with AWS Amplify.

| Objective | Technology & Concepts | Output Product |
| :--- | :--- | :--- |
| **Interface & UX** | Build core components (Chat UI, Profile Settings, Payment Gateways). | Intuitive, responsive user interface for services: Tarot Reading, Astrology Chart. |
| **Authentication** | Integrate AWS Cognito and Amplify Authenticator into the Frontend. | Fully functional sign-up/sign-in system. |
| **Backend Integration** | Write Client-side API calls (`axios`) functions to invoke the API Gateway Endpoint (Backend). | Working Fetch data/Post requests functions, displaying data (Response) from Lambda. |

### 2. Backend Development

This is the core of the Serverless architecture, focusing on business logic and performance optimization.

| Objective | Technology & Concepts | Output Product |
| :--- | :--- | :--- |
| **API & DB Layer** | Set up Express.js and **`serverless-http`** on AWS Lambda. Configure Prisma and secure connection to NeonDB. | Basic Lambda functions working: `UserAPI` (Profile CRUD) and `ReminderService`. |
| **Asynchronous Architecture** | Build the Reminder Service flow using EventBridge Scheduler and Amazon SES. | The `findUsersToRemind` logic is operational and automatically sends notification emails. |
| **Optimization** | Optimize the Serverless/Prisma deployment package, and set up minimum IAM Roles. | Backend source code is automatically deployed via GitHub Actions (CI/CD). |

### 4. AI Development

The most crucial part, where the RAG logic is established to deliver intelligent content.

| Objective | Technology & Concepts | Output Product |
| :--- | :--- | :--- |
| **RAG Core** | Understand and set up the Retrieval-Augmented Generation (RAG) flow. | Operational RAG architecture. |
| **Bedrock & Embeddings** | Use Amazon Bedrock to create Vector Embeddings from user questions and call LLMs. | The Lambda function (`ChatbotAPI`) successfully calls Bedrock to generate answers. |
| **Data Retrieval** | Use Pinecone as a Vector Database to store and retrieve knowledge Chunks from the RAG knowledge base (stored in S3). | The process of context retrieval and passing that context into the Prompt to generate accurate answers. |

#### Content

1. [Workshop overview](2.1-Workshop-overview)
2. [Frontend Development](2.2-Frontend-Development/)
3. [Backend Development](2.3-Backend-Development/)
4. [AI Development](2.4-AI-Development/)
