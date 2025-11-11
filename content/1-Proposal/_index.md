---
title: "Proposal"
weight: 1
chapter: false
pre: " <b> 1. </b> "
---

# SorcererXStreme: An AI-Powered Metaphysical Guidance Platform

## 1. Executive Summary

The SorcererXStreme AI platform is a unified AI-driven spiritual guidance system designed to help users explore self-discovery through various Eastern and Western esoteric disciplines, including **Astrology, Tarot, Numerology, and Eastern Horoscopes**. The foundation of the system is the **Retrieval-Augmented Generation (RAG) Core**, which ensures all output is grounded in curated esoteric knowledge sources.


## 2. Problem Statement

### What’s the Problem?

Users currently face several limitations when exploring spiritual and esoteric knowledge:

*   **Fragmented and Unverified Information:** Information is scattered across the internet and often lacks credibility or proper cross-referencing.
*   **Difficulty in Cross-Discipline Comparison:** Results are hard to compare between Eastern (e.g., Eastern Horoscope) and Western (e.g., Astrology) schools of thought.
*   **Lack of Personalization and Interaction:** Most applications offer static readings, lacking the depth of personalized dialogue and contextual advice.
*   **Shallow Content:** Many "fun" apps lack intellectual depth and robust knowledge.

### The Solution

SorcererXStreme AI offers a unified, intuitive, and intelligent platform:

*   **Direct Interaction:** Users chat directly with AI Chatbot, asking anything about their personality, fate, or relationships.
*   **RAG-Grounded Interpretations:** The core RAG system guarantees that interpretations are based on verified esoteric data, ensuring accuracy and depth.
*   **Tiered User Experience:** Free and VIP tiers optimize the user experience and create a revenue stream.
*   **Cost-Efficient Design:** A modern, lightweight design rapidly deployed on a cost-optimized AWS serverless architecture.

### Benefits and Return on Investment

| Benefit              | Impact                                                                       | Value                                     |
| :-------------------| :---------------------------------------------------------------------------- | :---------------------------------------|
| **Data Reliability** | RAG reduces AI "hallucinations" and provides verifiable interpretations.       | High Trust & better user retention.       |
| **Centralization**   | Consolidates Eastern and Western mystical data in one platform.                | Unified Knowledge base for users.         |
| **Monetization**     | VIP subscription model unlocks advanced features.                              | Stable Revenue stream and business viability. |
| **Operational Cost** | Serverless AWS architecture is used.                                         | Estimated $80–$90/month for MVP .         |

## 3. Solution Architecture

The SorcererXStreme AI platform utilizes a robust, hybrid serverless architecture on AWS, meticulously designed to handle real-time user interactions, scheduled tasks, and autonomous monitoring.

![Architecture Diagram](/images/High_Level_System_Architecture.drawio.png)

## System Architecture: SorcererXStreme

This architecture is built on AWS and organized into three distinct, interconnected functional flows: **Real-time API Interaction**, **Asynchronous Notification**, and **DevOps (CI/CD)**.

#### Summary of AWS Services

| Layer | AWS Service | Primary Role |
| :--- | :--- | :--- |
| **Networking & Edge** | Route 53, CloudFront, WAF | DNS Resolution, Caching/Acceleration, Security Traffic Filtering. |
| **Security & Identity** | Cognito, Secrets Manager | User Authentication Management, Securely storing API keys/sensitive credentials. |
| **Compute & API** | API Gateway, AWS Lambda, App Runner | Synchronous Entry Point, Serverless Business Logic Processing, Hosting Frontend/Containers. |
| **AI/ML Layer** | Amazon Bedrock | Provides **Embedding** models (RAG Retrieval) and **LLMs** (RAG Generation) for grounded content creation. |
| **Data & Storage** | RDS for PostgreSQL, DynamoDB, S3 | **RDS** (Vector Store, User Profiles), **DynamoDB** (Chat History/Fast Access Data), **S3** (Static Assets/RAG Knowledge Base). |
| **Async & Events** | EventBridge Scheduler, SQS, SES | Schedule-based triggering, Message Buffering, Email Sending. |
| **Monitoring & DevOps** | CloudWatch, SNS, CodePipeline/CodeBuild | Collecting Logs/Metrics, Urgent Alerting, Managing Automated CI/CD pipeline. |


#### Flow 1: Real-time API Interaction (Chat/RAG Inference)

This is the synchronous processing flow where users send a question and receive a generated answer from the LLM (using Retrieval-Augmented Generation - RAG).

| # | Activity | AWS Services | Detail & Role |
| :---: | :--- | :--- | :--- |
| **1** | **Ingress & Edge Filtering** | **User (1)** → **Route 53** → **CloudFront** → **WAF** | Request goes through **DNS resolution** (Route 53), is **accelerated & cached** (CloudFront), and undergoes **security checks** (WAF) before entering the region. |
| **2** | **Gateway & Authentication** | **WAF** → **API Gateway (4)** → **Cognito (3)** | Valid requests are **routed**. **Cognito** authenticates the user token. |
| **3** | **Orchestration** | **API Gateway (4)** → **Lambda (UserAPI) (5)** | `UserAPI` acts as the **orchestrator**, receiving the request, validating, and forwarding it. |
| **4** | **Vector Embedding (RAG S1)** | **Lambda (Chatbot) (5)** → **Bedrock (Embedding Model) (5)** | Converts the raw question into a **query\_vector** (semantic coordinates) via Bedrock (using NAT Gateway). |
| **5** | **Context Retrieval (RAG S2)** | **Lambda (Chatbot) (5)** → **RDS (PostgreSQL) (5)** | Sends the `query_vector` to RDS (using a vector extension) to perform **Hybrid Search** and retrieve the most relevant **context chunks**. |
| **6** | **Generation (RAG S3)** | **Lambda (Chatbot) (5)** → **Bedrock (LLM) (5)** | Sends the **Super Prompt** (Context + Original Question) to the **Bedrock LLM** to generate the final answer. |
| **7** | **History Logging (Background)** | **Lambda (Chatbot) (5)** → **DynamoDB (5)** | Logs chat/interpretation history to DynamoDB (via Endpoint, without waiting for a response). |
| **8** | **Completion** | **Lambda (Chatbot) (5)** → **API Gateway (4)** → **User (1)** | Returns the final answer to the user. |

#### Flow 2: Daily Horoscope Notification (Asynchronous)

This flow is triggered on a schedule (Scheduler) and uses SQS for message buffering and asynchronous processing.

| # | Activity | AWS Services | Detail & Role |
| :---: | :--- | :--- | :--- |
| **1** | **Schedule Trigger** | **EventBridge Scheduler (6)** → **Lambda (TriggerReminder)** | Initiates the flow at a pre-defined time. |
| **2** | **Fan-Out** | **Lambda** → **RDS** → **SQS (7)** | Retrieves the list of subscribed users from RDS and pushes individual messages into the **SQS Reminder Queue** for decoupled processing. |
| **3** | **Process & Deliver** | **SQS (8)** → **Lambda (SendReminder)** → **SES** | `SendReminderLambda` is triggered by SQS, creates the email content, and sends **notification emails** to users via Amazon SES. |


#### Flow 3: DevOps (CI/CD)

The automated process for deploying new source code from the repository to the AWS environment.

| # | Activity | AWS Services | Detail & Role |
| :---: | :--- | :--- | :--- |
| **1** | **Source & Activation** | **Developer (10)** → **GitHub (10)** → **CodePipeline (10)** | A `git push` activates the CodePipeline via a webhook. |
| **2** | **Build & Test** | **CodePipeline (10)** → **CodeBuild (10)** | Downloads code, installs dependencies, runs unit tests, and **packages (Builds)** the artifacts. |
| **3** | **Deployment** | **CodeBuild** → **CloudFormation** | Deploys the new version of Lambda functions and updates API Gateway and App Runner configuration. |

## 4. Technical Implementation

The SorcererXStreme project will follow an **Agile-Iterative Development** methodology, structured into a **9-week roadmap** comprising three main iterations (Iter 3, 4, and 5) after the initial planning phase.

### Implementation Phases

| Phase | Duration   | Focus Area |
| :----------------------------------------------------- | :--------------| :---------------------------------------------------------------------------------------------------------------------------------- |
| **I. Requirement Analysis & Documentation (Iter 3)**   | 3 Week         | Finalize SRS (v2) and SDS (v2) documents, proposal, setting the foundation for expanded roles and RAG.                                             |
| **II. Design & Expansion (Iter 4)**                   | 3 Week         | Core development of the RAG pipeline, implementation of the User Role system (Guest/Free/VIP), and configuration of the core AWS infrastructure.   |
| **III. AWS Integration & Testing (Iter 5)**           | 3 Week         | Full deployment on AWS, end-to-end testing, and performance optimization.                                                                          |
| **IV. Evaluation & Handoff**                          | Ongoing        | Optimization, performance reporting, and final beta release preparation.                                                                           |

### Technical Requirements & Deliverables by Iteration

**Iteration 3 – RAG Integration & System Redesign (Weeks 1–2–3)**

**Goal:** Re-analyze requirements, finalize the AWS architecture, define user experience flows, and prototype the RAG design.

| Category               | Key Tasks                                                                                                                                                                            | Responsibility   |
| :-------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------------------|
| **System Documentation**   | Review and update existing architecture. Write **SRS v2** (Functional & Non-functional) and **SDS v2**.                                                                                  | SE                   |
| **UX & Role Flow**         | Design detailed user flows for **Guest/Free/VIP** transitions. Define functional limits per role. Propose **VIP upgrade UI**.                                                            | SE                   |
| **RAG Architecture**       | Propose the RAG mechanism (data source, pipeline, embedding storage). Design RAG prototype pipeline: text -> embedding -> index. **Data collection** (Tarot, Horoscope, Numerology).   | AI                   |
| **AWS Infrastructure**     | Finalize **AWS Architecture Diagram**. Calculate **Cost Estimation** focusing on Free-tier and Serverless options.                              | SE, AI               |

**Deliverables:** Completed SRS v2, SDS v2, AWS Architecture Diagram with Cost Estimation, UX/Role Flow documentation, and RAG prototype design.

**Iteration 4 – User Roles & Production RAG Pipeline (Weeks 4–5–6)**

**Goal:** Implement the user authorization system and integrate the foundational RAG data corpus.

| Category                 | Key Tasks                                                                                                                                               | Responsibility   |
| :---------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------- | :-------------------- |
| **Authentication & Roles**   | Design the user schema (guest, free, vip). Integrate **AWS Cognito** into the frontend. Define API access permissions based on user role.   | SE                   |
| **VIP Functionality**        | Implement logic for **limiting chat counts, Tarot pulls,** and detailed Astrology views. Deploy pricing model and upgrade screens.          | SE                   |
| **RAG Production Prep**      | Cleanse and standardize data. Implement context retrieval by topic (love, career, zodiac). Build and store the initial **Corpus on S3**.    | AI                   |
| **AI Optimization**          | Fine-tune embedding model and chunk size. Write a script for **automated data updates** (S3 -> index).                                     | AI                   |

**Deliverables:** Fully functioning user role system (Guest/Free/VIP) integrated with Cognito. RAG mock API endpoints operational on local/dev environment, enabling testing of VIP access logic.

**Iteration 5 – AWS Deployment & Evaluation (Weeks 7 – 8 – 9)**

**Goal:** Complete full deployment on the cloud, perform comprehensive testing, and prepare for public release.

| Category               | Key Tasks                                                                                                                                                | Responsibility   |
| :--------------------------| :-------------------------------------------------------------------------------------------------------------------------------------------- | :--------------------|
| **AWS Infrastructure**     | Final configuration of **App Runner + Lambda**. Deploy **DynamoDB, S3, Cognito**. Create minimal **IAM Policies**.                           | SE                   |
| **Monitoring & Logging**   | Set up **CloudWatch** for Lambda and Cognito logging. Create a dashboard to monitor usage and costs.                                         | SE                   |
| **AI Model Tuning**        | Optimize **LLM prompt** and RAG pipeline. Implement **LLM token control** mechanisms for cost governance.                                    | AI                   |
| **Testing & Evaluation**   | Write **end-to-end test cases**. Comprehensive testing of roles, VIP restrictions, and RAG accuracy. Report on AI accuracy (pre/post-RAG).   | AI                   |

**Deliverables:** Stable system running on the AWS infrastructure. Performance, cost, and testing reports (**AWS Service Cost and Performance Sheet**). Readiness for public Beta testing.

## 5. Timeline & Milestones

The SorcererXStreme project will be executed over a concentrated **9-week development period** following the Agile-Iterative model.

### Project Timeline

| Iteration                          | Duration   | Weeks       | Primary Focus                             | Key Deliverables (Milestones)                                                                                                                                           |
| :-------------------------------------- | :--------------| :--------------- | :----------------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Iter 3: Redesign & RAG Prototype**   | 3 Weeks        | 1 – 2 **–** 3   | **Foundational Design & Documentation**         | **SRS v2** and **SDS v2, Proposal** finalized. **AWS Architecture Diagram** with Cost Estimation ready. RAG data collected and initial pipeline designed.                   |
| **Iter 4: Roles & VIP System**         | 3 Weeks        | 4 – 5 – 6       | **Core Logic Implementation & Authorization**   | **AWS Cognito** integrated for user authentication. Full **Guest/Free/VIP role logic** implemented and testable. RAG data corpus built on S3.                               |
| **Iter 5: AWS Deployment & QA**        | 3 Weeks        | 7 – 8 – 9       | **Cloud Deployment & Stabilization**            | System running **stably on AWS** (Amplify, Lambda, DynamoDB). Full end-to-end testing completed. **AWS Cost and Performance Sheet** finalized. Readiness for Beta Launch.   |


## 6. Budget Estimation

The project assume low usage for a non-production Demo and MVP environment (approx. 5,000 requests/month).

### Infrastructure Costs 

| Layer | AWS Service | Purpose | Estimated Monthly Cost (USD) - Paid |
| :---: | :--- | :--- | :---: |
| **I. COMPUTE & API** | | | |
| 1 | **AWS Lambda** | Backend Logic (RAG, Compute) | $0.22 |
| 2 | **Amazon API Gateway** | Synchronous Request Gateway | $0.03 |
| 3 | **AWS App Runner** | Host Frontend (Next.js) | $10.20 |
| **II. DATA & STORAGE** | | | |
| 4 | **RDS for PostgreSQL** | Relational Data/Profiles | $37.14 |
| 5 | **Amazon DynamoDB** | Chat History/Rate Limiting | $0.29 |
| 6 | **Amazon S3** | RAG Knowledge Base/Assets | $0.88 |
| **III. AI & SECURITY** | | | |
| 7 | **Amazon Bedrock** | Embedding & LLM/Content Generation | $2.45 |
| 8 | **Amazon Cognito** | Authentication/User Roles (MAUs) | $0.00 |
| 9 | **Secrets Manager** | Store Master Keys (Fixed Cost) | $2.15 |
| **IV. ASYNC & MONITORING** | | | |
| 10 | **EventBridge Scheduler** | Daily Horoscope Trigger | $0.00 |
| 11 | **Amazon SQS** | Notification Queue | $0.08 |
| 12 | **Amazon SES** | Email Delivery | $0.24 |
| 13 | **Amazon CloudWatch** | Logs/Metrics/Alarms | $4.46 |
| 14 | **Amazon SNS** | Alert Notifications | $0.00 |
| **V. NETWORK & DELIVERY** | | | |
| 15 | **Amazon Route 53** | DNS/Domain Management | $0.50 |
| 16 | **Amazon CloudFront** | CDN/Content Distribution/Low Latency | $0.24 |
| 17 | **AWS WAF** | Web Application Firewall (Layer 7 Protection) | $9.03 |
| **VI. DEVOPS & INFRASTRUCTURE** | | | |
| 19 | **AWS CodePipeline** | CI/CD (Automated Deployment) | $1.00 |
| 20 | **AWS CloudFormation** | Infra-as-Code (Resource Management) | $0.00 |

Link budget estimation: 
- [Compute + Monitor Layer + AI](https://drive.google.com/file/d/19rnxIgJZ9kt1rv7DiDIr483EZtEbwJoq/view?usp=sharing) 
- [Bedrock](https://drive.google.com/file/d/1ZqoGQjxSpZVKVjAOMy3Ps37iUDCm95k7/view?usp=drive_link)
- [Network + CICD](https://drive.google.com/file/d/19rnxIgJZ9kt1rv7DiDIr483EZtEbwJoq/view?usp=sharing)
 
### Total Project Cost: $67.73/month

## 7. Risk Assessment

### Risk Matrix

| Risk                        | Impact   | Probability   | Mitigation Strategy                                                                            |
| :-------------------------------| :------------| :---------------- | :------------------------------------------------------------------------------------------------- |
| **LLM Hallucination**           | High         | Medium            | Implement **RAG Fact Checker**; use high-quality LLMs; ground answers in verified sources.         |
| **Cost Overruns (LLM Calls)**   | High         | Medium            | Set up **AWS Budget Alerts**; implement **token control**; use tiered LLM models (Free vs. VIP).   |
| **RAG Retrieval Latency**       | Medium       | Medium            | Optimize RAG indexing (FAISS); optimize chunk size and embedding model choice.                     |
| **Security Breaches**           | High         | Low               | Use **Cognito** for authentication and **Secret Manager** for credential handling.                 |

## 8. Expected Outcomes

### Technical Improvements

*   **Real-time Accuracy:** RAG integration significantly reduces AI "hallucinations," enhancing the reliability of interpretations.
*   **Scalability:** The serverless AWS architecture ensures automatic scaling to handle significant user traffic.

### Long-term Value

*   **Monetization:** The VIP subscription model creates a clear, stable path for revenue generation.
*   **Data Foundation:** A proprietary, verified esoteric knowledge base (RAG corpus) is established as a valuable, reusable asset.
*   **Future Expansion:** The flexible AWS architecture (Lambda, Bedrock) is easily upgradable for mobile apps (React Native) or voice chat features.

