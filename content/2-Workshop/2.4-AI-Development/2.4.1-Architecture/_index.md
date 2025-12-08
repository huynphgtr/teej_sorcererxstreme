---
title : "Architecture & Tech Stack"
weight : 1
chapter : false
pre : " <b> 2.4.1. </b> "
---

The system is built upon a **Serverless architecture on AWS** to optimize operational costs and scalability.

## 1. Core Technology Stack

| Component | Selected Technology | Rationale |
| :---: | :---: | :--- |
| **Language** | Python 3.x | **Specialized AI Ecosystem.** The optimal deployment environment for LLMs, Embeddings, and RAG workflows. |
| **Compute** | AWS Lambda | **Event-Driven Serverless Architecture.** Optimizes operational costs (pay-per-request) and offers seamless integration with the AWS ecosystem (S3, API Gateway). |
| **Raw Storage** | Amazon S3 | **Durable & Cost-Effective Storage.** Securely stores raw data, integrated directly with Lambda automated processing triggers. |
| **Vector DB** | Pinecone | **Managed Service.** Specialized for Vector Search, offering high query speeds, low latency, and zero infrastructure management. |
| **Meta DB** | Amazon DynamoDB | **Millisecond Latency.** Optimized for Exact Match queries and storing conversation history with instant response speeds. |
| **AI Model** | Amazon Bedrock | **Unified API.** Access to diverse Foundation Models via a single gateway, ensuring absolute data privacy and security. |
| **CI/CD** | GitHub Actions | **Automation.** Automates the Testing and Deployment process to Lambda immediately upon code push. |

## 2. Resources & Environment Setup

Before proceeding with the detailed implementation, ensure the following resources and access rights are established:

1.  **AWS Account & Region:**
    * An active AWS account with billing enabled.

2.  **Third-Party Services:**
    * **Pinecone:** Create an account and generate an **API Key** (Serverless Index).
    * **GitHub:** Configure **GitHub Secrets** to securely store credentials (AWS_ACCESS_KEY, PINECONE_API_KEY) for CI/CD pipelines.

3.  **Local Environment:**
    * Install **AWS CLI v2** and run `aws configure`.
    * Install **Python 3.9+** and **SAM CLI** (Serverless Application Model) for building and deploying Lambda functions.