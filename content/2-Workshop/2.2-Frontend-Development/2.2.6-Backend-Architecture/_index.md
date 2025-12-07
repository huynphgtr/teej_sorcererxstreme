---
title: "Backend Reference Architecture (RAG & Database)"
weight: 6
chapter: false
pre: " <b> 2.2.6. </b> "
---


As a modern Frontend Developer, the line between Frontend and Backend is blurring. You don't need to write SQL or manage servers, but you usually must **understand** the data flow to integrate effectively.

The SorcererXtreme system uses an advanced **RAG (Retrieval-Augmented Generation)** architecture. Let's dissect what happens after you call `fetch('/api/chat')`.

### 1. RAG Flow: Why is the AI accurate?

If we simply send the question to ChatGPT, it might "hallucinate" based on old training data. To make the AI act like a "Wise Sorcerer" knowledgeable in Tarot, we use a 4-step process:

1.  **Retrieval:**
    *   When User asks *"What does The Fool card mean?"*, the question is converted into a vector (numbers).
    *   The system searches **Pinecone** (Vector DB) for Tarot book passages with the most similar meaning.
2.  **Augmentation:**
    *   The system combines the original question + found content from Pinecone into a complete Prompt.
    *   *Prompt: "Based on the following knowledge [book excerpt...], answer the question: What does The Fool card mean?"*
3.  **Generation:**
    *   Send this Prompt to **Amazon Bedrock** (hosting Claude 3 or Titan models).
    *   AI answers based strictly on the provided text, avoiding fabrication.
4.  **Response:** Frontend receives the final answer and renders it.

### 2. "Polyglot Persistence" Strategy

A large application never uses just one type of Database. We use the right tool for the right job:

**A. NeonDB (Serverless PostgreSQL)**
*   **Type:** Relational.
*   **Data:** User Profiles, VIP tiers, Payment transactions.
*   **Why:** Financial data requires the highest integrity (ACID). SQL is the #1 choice.

**B. Amazon DynamoDB**
*   **Type:** NoSQL (Key-Value).
*   **Data:** Chat History, Activity Logs.
*   **Why:** Chat generates millions of records. DynamoDB offers limitless scale with single-digit millisecond latency.

**C. Pinecone**
*   **Type:** Vector Database.
*   **Data:** Tarot/Astrology Knowledge (encoded as Vectors).
*   **Why:** SQL or NoSQL cannot search by "meaning" (semantic search). Only a Vector DB understands that "King of Coins" and "King of Pentacles" are related.

### 3. Security: The "Fortress" Model

Your Frontend (`sorcererxtreme.vn`) is public land. But the Backend is a sanctuary.

*   **No Exposed Databases:** NeonDB or Pinecone never open ports to the Internet.
*   **API Gateway as the Guard:** All requests must pass through API Gateway. It checks the "ID Badge" (Cognito Token). If missing or expired -> Block immediately (401 Unauthorized).
*   **Lambda as the Courier:** Only Lambda functions (residing in a private VPC) hold the Secret Keys to unlock the Database doors.

---
{{% notice info %}}
**Takeaway:** When debugging "why did the AI give a wrong answer?", a Frontend Dev can guess: "Ah, maybe the **Retrieval** step in Pinecone found the wrong context", instead of blaming the AI Model. Understanding the system helps you fix bugs faster!
{{% /notice %}}
