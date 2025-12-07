---
title: "Frontend Development"
weight: 2
chapter: false
pre: " <b> 2.2. </b> "
---

# Building Serverless Frontend with Next.js & AWS Amplify

## 1. Workshop Overview

Welcome to the **Building Modern Serverless Frontend** workshop for the **SorcererXtreme** project.

In this project, the Frontend acts as the "face" of the application, where users directly interact with the mystical features. Our mission is to build a beautiful, smooth interface that communicates effectively with the backend AWS services.

## 2. Frontend Architecture

We will focus on the Client (Frontend) architecture and its Integration Points:

![Frontend Architecture Diagram](/images/frontend_architecture_diagram.png)

### Frontend Workflow:

1.  **Hosting & Delivery:** Next.js code is hosted and operated on **AWS Amplify**. Users access the web app via a global CDN (CloudFront) built into Amplify, ensuring lightning-fast load times.
2.  **Authentication:** When a user Logs In, the Frontend communicates directly with **Amazon Cognito**. Cognito returns a "Token" (like an access pass).
3.  **API Interaction:** For every request (like chatbot or Tarot reading), the Frontend sends this Token along with the request to **Amazon API Gateway**.
4.  **Response:** The Frontend receives JSON results from the API and renders the UI. The Frontend **does not need to know** what Database or AI model is behind the API; it only cares about the Input (Request) and Output (Response).

## 3. Frontend Tech Stack

The "weaponry" of a Frontend Developer in this project:

| Technology | Role | Why use it? |
| :--- | :--- | :--- |
| **Next.js (App Router)** | Framework | Strong Server-Side Rendering (SSR) for SEO, powerful Router. |
| **AWS Amplify (Gen 2)** | Platform | Provides Hosting, automated CI/CD, and fast Cloud connection libraries. |
| **Tailwind CSS** | Styling | Rapid styling, easy customization for the mystical "Dark Mode". |
| **Framer Motion** | Animation | Create smooth motion effects (like 3D Tarot card flipping). |
| **Amplify UI** | Library | Pre-built components for Login/Registration flows. |
| **Axios / Fetch** | HTTP Client | Used to call API Gateway. |

## 4. Estimated Time & Cost

| Item | Details |
| :--- | :--- |
| **Time** | 2-3 hours per day |
| **Cost** | **~$9.06/month** (Total project) |

## 5. Workshop Content

We will follow a standard Frontend development process:

1.  [**Preparation:**](5.1-Preparation/) Setup Next.js and Amplify.
2.  [**UI Implementation:**](5.2-UI-Implementation/) Code Chat & Tarot UI with animations.
3.  [**Integration:**](5.3-Integration/) Integrate Login (Cognito) and API calls (Gateway).
4.  [**CI/CD Pipeline:**](5.4-Deployment/) Push code to Git and auto-deploy to the Internet.
5.  [**Advanced:**](5.5-Advanced-Deployment/) Custom Domain setup and SEO optimization.
6.  [**Backend Reference:**](5.6-Backend-Architecture/) Understanding RAG model.
7.  [**Cleanup:**](5.7-Cleanup/) Cleaning up resources.

---
{{% notice tip %}}
**Frontend Mindset:** In Serverless architecture, Frontend is not just a "renderer". It is also responsible for **Security** (keeping Tokens safe) and **User Experience** (handling Loading states while waiting for AI). Pay attention to these points during the workshop!
{{% /notice %}}