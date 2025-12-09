---

title: "Proposal"
weight: 1
chapter: false
pre: " <b> 1. </b> "

---
<img src="/images/logo.jpg" class="img-responsive" style="max-width:300px; display:block; margin:auto;">

# AWS FIRST CLOUD AI JOURNEY – PROJECT PLAN
# TEEJ_SorcererXStreme - FPT University - Ho Chi Minh Campus - SORCERERXSTREME

---

## 0. TABLE OF CONTENTS

1. [CONTEXT AND MOTIVATION](#1-context-and-motivation)
   * 1.1. [Summary](#11-summary)
   * 1.2. [Project Success Criteria](#12-project-success-criteria)
   * 1.3. [Assumptions and Premises](#13-assumptions-and-premises)

2. [SOLUTION ARCHITECTURE](#2-solution-architecture)
   * 2.1. [Technical Architecture Diagram](#21-technical-architecture-diagram)
   * 2.2. [Technical Plan](#22-technical-plan)
   * 2.3. [Project Plan](#23-project-plan)
   * 2.4. [Security Factors](#24-security-factors)

3. [ACTIVITIES AND DELIVERABLES](#3-activities-and-deliverables)
   * 3.1. [Activities and Deliverables](#31-activities-and-deliverables)
   * 3.2. [Out of Scope](#32-out-of-scope)
   * 3.3. [Operational Handover Process](#33-operational-handover-process)

4. [COST ANALYSIS BY SERVICE](#4-cost-analysis-by-service)

5. [TEAM](#5-team)

6. [RESOURCES AND COST ESTIMATION](#6-resources-and-cost-estimation)

7. [ACCEPTANCE](#7-acceptance)

---

## 1. CONTEXT AND MOTIVATION

### 1.1 Summary
#### 1. Customer Context & Problem 

* **Problem:** Current sources of spiritual information are fragmented, unverified, and lack personalization. Users struggle to find deep, reliable interpretations or compare Eastern vs. Western schools of knowledge (e.g., Eastern Horoscope vs. Astrology).
* **Motivation:** The urgent need of current customers is to build a unified platform capable of providing intellectually verified content while maintaining scalability and optimizing operational costs.


#### 2. Business & Technical Goals 

<table>
  <thead>
    <tr>
      <th style="width: 20%;">Goal Type</th>      
      <th style="width: 70%;">Details</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Business</strong></td>
      <td> - Provide content with superior reliability and depth compared to current services. <br> - Generate revenue from a paid service model.</td>
    </tr>
    <tr>
      <td><strong>Technical</strong></td>
      <td>- Ensure AI Reliability: Apply RAG core to minimize AI "hallucinations". <br> - Scalability: Build AWS Serverless architecture (Lambda, API Gateway) to easily handle high traffic and optimize operational costs (Pay-per-use).</td>
    </tr>
  </tbody>
</table>


#### 3. Use Cases 

Key functions the project will support for users:

* **Deep Interactive AI Chat:** Chat directly with AI, capable of maintaining context and combining multiple schools of thought in a single session.
* **Personalized Interpretation:** Provide in-depth reports based on input data (date of birth, place of birth, time of birth).
* **Automated Notifications:** Send periodic notifications via email.

#### 4. Consulting Service Summary

Professional services will be provided to achieve the above goals, including:

* **Serverless Architecture Design:** Build a multi-model architecture and set up RAG flow on AWS Bedrock.
* **Cost & Performance Optimization:** Fine-tune Lambda functions and establish security via SSM Parameter Store.
* **CI/CD Automation:** Implement the entire development and deployment process automatically (IaC) using Serverless Framework and GitHub Actions.

### 1.2 Project Success Criteria
The success of the project will be evaluated based on the following quantitative and qualitative criteria:
* **Data Reliability:** The RAG system operates accurately, minimizing AI hallucinations, and providing transparent source citations for interpretations.
* **Knowledge Unification:** Successful integration of Eastern and Western metaphysical data into a single platform.
* **Business Efficiency:** Successful implementation of a tiered model (Free/VIP) to generate a stable revenue stream.
* **Cost Optimization:** System operates on Serverless architecture with an estimated cost of ~$9.06/month for the Demo environment.
* **Scalability:** System auto-scales to handle high traffic without manual intervention.

### 1.3 Assumptions and Premises
The project is executed based on the following assumptions and constraints:
* **Technology Dependency:** The project depends on the availability and stability of AWS services (Bedrock, Lambda, API Gateway).
* **Data:** It is assumed that input data for RAG (books, metaphysical documents) is clean, copyrighted, or within valid usage scope.
* **Risks:** There is a risk of LLM "hallucinations" despite using RAG; a Fact Checker mechanism is required.
* **Cost Constraints:** The operating budget is strictly optimized.

---

## 2. SOLUTION ARCHITECTURE

### 2.1 Technical Architecture Diagram
The proposed architecture is **Hybrid Serverless** on AWS, including layers: Edge & Auth, API & Routing, Compute, Data, AI/ML, and Async Monitoring.

![Architecture Diagram](/images/High_Level_System_Architecture.drawio(2).png)

**Key Components:**
* **Frontend:** AWS Amplify (Next.js).
* **Auth:** Amazon Cognito.
* **Backend:** AWS Lambda, Amazon API Gateway.
* **Database:** NeonDB (PostgreSQL) as main DB, DynamoDB for history, Pinecone for Vector Search.
* **AI Core:** Amazon Bedrock (LLM & Embeddings), S3 (RAG Docs).

### 2.2 Technical Plan
The project team will develop and deploy the system according to the following technical process:
* **Scripts & IaC:** Use **Serverless Framework** to generate CloudFormation templates, ensuring infrastructure deployment (IaC) is repeatable and consistent.
* **CI/CD:** Use **GitHub Actions** to automate the Build, Test, and Deploy process for Lambda functions and API Gateway.
* **RAG Pipeline:** Set up data processing flow: Upload documents to S3 -> Lambda Trigger -> Generate Embedding (Bedrock) -> Store in Pinecone Vector DB.

### 2.3 Project Plan
The project applies the **Agile-Iterative** model over 9 weeks, divided into 3 main phases (Iterations):
* **Iter 3 (Weeks 1-3):** Redesign system, finalize SRS/SDS documentation, and build Prototype for RAG pipeline.
* **Iter 4 (Weeks 4-6):** Integrate AWS Cognito, develop permission logic (Guest/VIP), build data corpus on S3.
* **Iter 5 (Weeks 7-9):** Full deployment to AWS, End-to-End testing (QA), cost and performance optimization.

### 2.4 Security Factors
Security is designed according to the "Defense in Depth" model:
* **Identity & Access:** Use Amazon Cognito to manage identity and user permissions (User Roles).
* **Data Protection:** Secret keys (API Keys, DB Credentials) are stored securely in **AWS Systems Manager Parameter Store**, not hardcoded.
* **Network Security:** API Gateway acts as the sole entry point.
* **Monitoring:** Use CloudWatch to log and monitor abnormal behaviors.

---

## 3. ACTIVITIES AND DELIVERABLES

### 3.1 Activities and deliverables

| Deployment Phase | Timeline | Activities | Milestones | Completion Date |
| :--- | :--- | :--- | :--- | :--- |
| **Design & Prototype** | Weeks 1-3 | - Design AWS architecture.<br>- Collect RAG data.<br>- Write SRS/SDS documentation. | - Architecture Diagram & Cost Table.<br>- RAG Pipeline Prototype.<br>- Project Proposal Document. | 12/10/-01/11/2025 |
| **Development (Core)** | Weeks 4-6 | - Integrate Cognito.<br>- Code Backend (Lambda).<br>- Build Vector DB. | - Auth System operational.<br>- Complete API for VIP/Free.<br>- RAG Knowledge Base ready. | 02/11-22/11/2025 |
| **Deployment & QA** | Weeks 7-9 | - Configure GitHub Actions.<br>- Deploy to Prod environment.<br>- Load Test & Pen Test. | - System Live on AWS.<br>- Test Report.<br>- User Guide. | 23/11-09/12/2025 |

### 3.2 Out of Scope
The following items are outside the scope of the current project (MVP):
* Mobile App development (iOS/Android).
* Real-time Voice Chat feature.

### 3.3 Operational Handover Process
The current version is an MVP (Minimum Viable Product). To bring it to large-scale production, additional steps are required:
* **Feature Expansion:** Upgrade Lambda and Bedrock architecture to support React Native Mobile App or Voice Chat in the future.
* **Operational Excellence:** Set up more detailed AWS CloudWatch Alarms to monitor errors and latency.
* **RAG Optimization:** Fine-tune chunk sizes and retrieval strategies to reduce response latency.

---

## 4. COST ANALYSIS BY SERVICE

Estimated cost for Demo environment (~5,000 requests/month) is **$9.06/month**.

Detailed link: [Cost Estimation Table](https://drive.google.com/file/d/1Cy0ivN1wIOJ8DthIOE4cYYFG1BzESt5m/view?usp=sharing)

| Layer | AWS Service | Purpose | Cost/Month |
| :--- | :--- | :--- | :--- |
| **Compute & API** | AWS Lambda, API Gateway, Amplify | Backend Logic & Hosting | ~$2.62 |
| **Data & Storage** | DynamoDB, S3 | Chat History & RAG Storage | ~$0.92 |
| **AI & Security** | Bedrock, Cognito, Parameter Store | LLM Generation & Auth | ~$2.65 |
| **Async & Monitoring** | SES, CloudWatch, EventBridge | Email & Logging | ~$2.88 |
| **Total** | | | **$9.06** |

---

## 5. TEAM

### Overall Responsibility

| Name | Title | Description | Email |
| :--- | :--- | :--- | :--- |
| *Nguyễn Gia Hưng* | *Head of Architecture Solution* | *Design and develop cloud-native and serverless platforms* | *hunggia@amazon.com* |

### Stakeholders

| Name | Title | Description | Email |
| :--- | :--- | :--- | :--- |
| *Đình Quang Sáng* | PQHDN | Evaluation & Direction | *SangDQ6@fe.edu.vn* |

### Support Representatives

| Name | Title | Description | Email |
| :--- | :--- | :--- | :--- |
| *Văn Hoàng Kha*| Cloud Security Engineer, Co-founded and led Viet-AWS | Execute technical directions on Cloud Security and DevSecOps | *khavan.work@gmail.com* |


### Implementation Team

| Name | Title | Description | Email |
| :--- | :--- | :--- | :--- |
| *Trần Phương Huyền* | Leader + Backend Dev | Project Management + Backend Development | *tranphuonghuyen2005@gmail.com* |
| *Nguyễn Lâm Anh* | AI Dev | AI Development | *nguyenla110505@gmail.com* |
| *Nguyễn Văn Linh* | AI Dev | AI Development | *nguyenvanlinh.1710.it@gmail.com* |
| *Bùi Nguyễn Tấn Khang* | Frontend Dev | Frontend Development | *tankhang6a6@gmail.com* |


---

## 6. RESOURCES AND COST ESTIMATION

### Personnel Unit Price
*(Estimated personnel unit price based on student/academic project rates)*

| Resource / Role | Responsibilities | Unit Price (USD) / Hour |
| :--- | :--- | :--- |
| **Solution Architects** | - Overall architecture design (Hybrid Serverless, RAG Flow).<br>- Selection of optimal AWS services (Bedrock, Lambda, Pinecone).<br>- Ensuring non-functional requirements (security, latency, cost). | **2.3** |
| **Software Engineers** | - Frontend development (Next.js/Amplify) and Cognito integration.<br>- Backend API (Lambda) construction to handle user logic.<br>- Chat History storage management (DynamoDB). | **0.7** |
| **AI Engineers** | - Fine-tune Prompts for Bedrock for Tarot/Horoscope interpretation.<br>- Build RAG flow: Document Chunking, Embeddings, Vector Search.<br>- Evaluate accuracy and minimize AI hallucinations. | **0.7** |

### Estimated Hours & Cost by Phase
*(Effort estimation for 3 Iterations - 9 weeks)*

| Project Phase | Role | Man-hours | Cost (USD) | Total Phase Cost |
| :--- | :--- | :--- | :--- | :--- |
| **Iter 3: Design & Prototype**<br>*(Weeks 1-3)* | **Solution Architect**<br>Software Engineer<br>AI Engineer | 30<br>30<br>30 | $69.0<br>$21.0<br>$21.0 | **$111.0** |
| **Iter 4: Core Development**<br>*(Weeks 4-6)* | Solution Architect<br>**Software Engineer**<br>**AI Engineer** | 20<br>80<br>80 | $46.0<br>$56.0<br>$56.0 | **$158.0** |
| **Iter 5: Deploy & QA**<br>*(Weeks 7-9)* | Solution Architect<br>Software Engineer<br>AI Engineer | 10<br>60<br>40 | $23.0<br>$42.0<br>$28.0 | **$93.0** |
| **TOTAL** | | **380** | | **$362.0** |

### Cost Contribution Allocation
*(Allocation of cost responsibility among stakeholders)*

| Participating Party | Contribution Value (USD) | Contribution Ratio of Total |
| :--- | :--- | :--- |
| **Partner (Team)** | **$362.0** | **97.5%** (Self-contributed personnel cost) |
| **AWS** | **~$9.06/month** | **2.5%** (Estimated Cloud infrastructure cost) |
| **Customer** | **$0** | **0%** (Academic Project/MVP) |

---

## 7. ACCEPTANCE

The acceptance of the **SorcererXStreme** project will not only be based on feature completion but also on system stability and the quality of AI output.

### 7.1. Deliverable Package 
The product is considered ready for acceptance when the project team provides all the following items:
* **Source Code:** Repository containing all Backend code (Lambda), Frontend code (Next.js), and Infrastructure as Code (Serverless Framework).
* **Technical Documentation:** Including SRS, SDS, API Documentation, and Deployment Guide.
* **Live Environment:** URL to access the stable functioning Demo environment on AWS.
* **Quality Report:** Test results (Test Cases) and accuracy assessment report of the RAG model.

### 7.2. Acceptance Process 
The process will take place in a 4-step sequence:
1.  **Live Demo:** The project team directly demos key User Flows: Registration -> Select Service -> Chat with AI -> Receive Results.
2.  **User Acceptance Testing (UAT):** The Customer/Mentor directly uses the system (03-05 days) to check the accuracy of spiritual knowledge and load capacity.
3.  **Feedback & Fix:** The project team commits to fixing Critical errors within 24-48 hours. Minor errors will be updated in subsequent patches.
4.  **Completion Confirmation:** The project is accepted when there are no critical errors and core features operate as committed.

### 7.3. Rejection Conditions 
The product will not be accepted if:
* **Deployment Error:** System Downtime or API error rate > 10%.
* **Content Deviation:** AI provides seriously misleading information or violates safety rules.
* **Over Budget:** Actual operating costs exceed the allowable threshold without reasonable explanation.