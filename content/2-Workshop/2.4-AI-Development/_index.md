---
title : "AI Development"
weight : 4
chapter : false
pre : " <b> 2.4. </b> "
---

## Introduction

This document details the comprehensive process of environment setup, data standardization, and the construction of the AI processing pipeline (Embedding, RAG, Prompt Engineering, LLM) for a multi-domain consulting system covering Tarot, Numerology, Horoscopes, and Astrology. The system's objective is to provide logical, consistent, and controlled responses based on a precise Knowledge Base, thereby minimizing AI hallucinations.

This report also delves into the technical challenges encountered (specifically regarding Vietnamese language processing) and provides a rationale for technology selection decisions (Trade-off analysis).

---

## 1. Architecture & Technology Overview

The system is built upon a **Serverless architecture on AWS** to optimize operational costs and scalability.

### Core Technology Stack

| Component | Selected Technology | Rationale |
| :---: | :---: | :--- |
| **Language** | Python 3.x | **Specialized AI Ecosystem.** The optimal deployment environment for LLMs, Embeddings, and RAG workflows. |
| **Compute** | AWS Lambda | **Event-Driven Serverless Architecture.** Optimizes operational costs (pay-per-request) and offers seamless integration with the AWS ecosystem (S3, API Gateway). |
| **Raw Storage** | Amazon S3 | **Durable & Cost-Effective Storage.** Securely stores raw data, integrated directly with Lambda automated processing triggers. |
| **Vector DB** | Pinecone | **Managed Service.** Specialized for Vector Search, offering high query speeds, low latency, and zero infrastructure management. |
| **Meta DB** | Amazon DynamoDB | **Millisecond Latency.** Optimized for Exact Match queries and storing conversation history with instant response speeds. |
| **AI Model** | Amazon Bedrock | **Unified API.** Access to diverse Foundation Models via a single gateway, ensuring absolute data privacy and security. |
| **CI/CD** | GitHub Actions | **Automation.** Automates the Testing and Deployment process to Lambda immediately upon code push. |

---

## 2. Data Strategy

This section required the most intensive research effort to ensure the quality of AI responses (*Garbage In, Garbage Out*).

### Format Standardization Journey: From JSON to JSONL

- The choice of file storage format plays a decisive role in processing performance and AWS Lambda memory costs.
- After extensive testing, the system standardized the entire Knowledge Base (Tarot, Horoscope, Numerology) to the **JSONL (`.jsonl`)** format. In this format, each line of the file is a distinct, valid JSON object, independent of the others.

#### Why JSONL?

* **Memory Optimization:** Utilizes **Lazy Loading** mechanisms instead of loading the entire file. This keeps Lambda RAM consumption low and stable, completely eliminating Out of Memory (**OOM**) errors with large datasets.
* **Local Fault Tolerance:** A syntax error in one line does not break the entire pipeline. The system automatically skips the erroneous line and continues processing, ensuring data flow continuity.
* **Streaming Compatibility:** Supports direct stream reading from S3, minimizing latency when initiating Batch processing.

#### Comparison with Legacy Solution (Standard JSON)

The initial standard JSON format revealed critical performance weaknesses:

| Feature | Legacy Solution: Standard JSON (`.json`) | Current Solution: JSONL (`.jsonl`) |
| :--- | :--- | :--- |
| **Structure** | - Monolithic Array. <br> - Enclosed by `[...]`, separated by commas. | - Independent Lines. <br> - Each line is a separate object. |
| **Performance** | - Memory Hog: Must load the entire file into RAM to parse the DOM. | - Memory Safe: Processing consumes RAM per line only. |
| **Risk** | - Single Point of Failure: One missing comma = Entire file corrupted. | - Isolated: Error in line 1 does not affect line 2. |

**Structure Illustration:**

* **JSON (Legacy - Array):**
    ```json
    [ {"id": 1, "text": "Aries..."}, {"id": 2, "text": "Taurus..."} ]
    ```
![JSON](/static/images/json.png)
*Figure 2.1: Data sample in standard JSON format.*

* **JSONL (New - Line-delimited):**
    ```json
    {"id": 1, "text": "Aries..."}
    {"id": 2, "text": "Taurus..."}
    ```
![JSONL](/static/images/jsonl.png)
*Figure 2.2: Data configuration in JSONL format.*

### "Divide and Conquer" Technique in Embedding

* **Challenge:** Raw text data is often too long and contains significant keyword noise. When RAG (Retrieval-Augmented Generation) queries entire large paragraphs, the AI's focus is easily "diluted," leading to rambling responses.
* **Solution:** Instead of embedding the entire text, the system implements:
    * *Flatten Contexts:* Breaks down information fields within contexts (e.g., `strengths`, `weaknesses`, `love`).
    * *Meta-Injection:* Attaches metadata (Name, Category, Keyword) to each micro-chunk before embedding.
* **Result:** During vector search, the system extracts the precise segment required (e.g., retrieving only the "Love aspects of Aries" segment rather than the entire Aries article), enabling the LLM to provide focused, accurate answers.

---

## 3. Storage & Retrieval

The system utilizes a **Hybrid Retrieval** mechanism (combining Vector Search and Key-Value Lookup). The core of this architecture is the use of Pinecone as the Long-term Memory for the AI.

### Vector Database: The Power of Pinecone

Instead of self-managing infrastructure, the project selected **Pinecone**—a specialized Managed Vector Database. This component is crucial for enabling the AI to "understand" the semantics of Tarot or Horoscope inquiries, rather than relying solely on keyword matching.

#### Index Configuration 
Based on the Serverless architecture, the system utilizes Pinecone's **Serverless Index** mode to optimize costs (paying only for data read/write, with no server maintenance fees).

![Pinecone Index Configuration](/static/images/pinecone1.jpg)
*Figure 3.1: Actual Index Configuration on Pinecone Console.*

**Key Technical Specifications:**
* **Dimensions:** **1024.** This vector length is sufficient to encode the complex semantic nuances of metaphysical texts while remaining more optimal than 4096-dimensional models (too heavy) or 768-dimensional models (potentially lacking detail).
* **Metric:** **Cosine Similarity.** The system uses Cosine to measure the angle between two vectors. In semantic space, the smaller the angle between two vectors (Cosine approaching 1), the more similar their meanings. This metric is best suited for NLP (Natural Language Processing) tasks compared to Euclidean distance.
* **Pod Type:** Serverless (Automatically scales based on demand, no pre-provisioning required).

#### Record Structure
The true power of Pinecone lies in its ability to combine **Vector Search** with **Metadata Filtering**.

![Vector Record Structure](/static/images/pinecone1.jpg)
*Figure 3.2: Detail of a Vector Record including ID, Values, and Metadata.*

Each stored Record consists of three parts:
1.  **ID:** Unique identifier (Hashed from original content) to prevent data duplication (Dedup).
2.  **Values (Vector):** An array of 1024 floating-point numbers, representing the meaning of the text segment.
3.  **Metadata:** This is the most critical part for increasing Accuracy. Instead of searching the "entire ocean," the AI uses metadata to narrow the scope.
    * *Example:* When a user asks about "Leo's Love Life," the system filters by `category: "zodiac"` and `entity_name: "Leo"` first, before finding the nearest vector. This completely eliminates the possibility of the AI retrieving information for the wrong sign.

### Trade-off Analysis: Why Pinecone over AWS RDS (pgvector)?

In the AWS ecosystem, the standard solution is often **Amazon RDS for PostgreSQL** with the `pgvector` extension. However, after rigorous trade-off consideration, Pinecone was selected.

Below is the detailed comparison analysis:

| Criteria | Pinecone (Managed SaaS) | Amazon RDS + pgvector (Self-Managed) |
| :--- | :--- | :--- |
| **Architecture** | **Native Vector DB.** Designed from the core specifically for high-speed vector storage and retrieval. | **Relational DB + Extension.** A traditional relational database with vector processing capabilities "bolted on." |
| **Operations (Ops)** | **Zero Ops.** No server management, no manual index tuning. Pure API calls. | **High Ops.** Requires instance management, version updates, manual index configuration (IVFFlat/HNSW), and periodic database vacuuming. |
| **Latency** | **Ultra-low (<50ms).** Optimized for large-scale vector queries. | Dependent on hardware configuration (CPU/RAM) and index tuning. Prone to slowness with large data if not well-optimized. |
| **Cost** | **Pay-as-you-go.** Billed based on stored vectors and R/W operations. Very cost-effective for project initiation. | **Fixed Hourly Cost.** Must pay for instances running 24/7 even with zero usage (unless using Aurora Serverless v2, which is costly). |
| **Filtering** | **Native Pre-filtering.** Supports filtering metadata *before* vector search (Single-stage filtering), which is very powerful. | Requires combining SQL `WHERE` clauses with vector search, which can sometimes impact performance if indexing is not precise. |

**Conclusion:** Given the current project scale and the requirement for rapid deployment (Time-to-market), **Pinecone** is the optimal choice due to its Serverless nature, allowing the team to focus on AI feature development rather than Database Administration (DBA) tasks.

### Why is DynamoDB still needed?
Although Pinecone is powerful for Semantic Search, the system still requires DynamoDB for:
* **Exact Match Retrieval:** Lambda Metaphysical needs to retrieve absolutely precise information based on IDs (e.g., the meaning of "The Fool" card when drawn, or specific star information in "Tu Vi"). DynamoDB performs this faster and cheaper than vector search.
* **Latency Reduction:** Offloads non-inferential tasks from the Vector DB.
* **Raw Dataset Storage:** Serves as a backup source and allows for quick metadata lookup without vector decoding.

---

## 4. AI Models & RAG Pipeline

**Amazon Bedrock** is utilized as the centralized connection gateway.

### Embedding Model: Cohere Embed Multilingual
* **Selection:** `cohere.embed-multilingual-v3`.
* **Rationale:**
    * Superior Vietnamese language support compared to early Titan models.
    * Better capability to capture **Semantic Nuances** in metaphysical/spiritual texts.
    * Vector size (1024 dimensions) balances accuracy with storage costs.

### Large Language Model (LLM): Amazon Nova Pro
* **Selection:** `amazon.nova-pro-v1:0`.
* **Rationale:**
    * *Reasoning Capability:* Strong logical reasoning, suitable for synthesizing information from disparate RAG fragments (e.g., weaving the meanings of 3 Tarot cards into a single narrative).
    * *Context Window:* Sufficiently large to hold complex prompts and chat history.
    * *Cost/Performance:* Delivers high performance at a more reasonable cost compared to other high-end commercial models.

---

## 5. Lessons Learned & Development Roadmap

### Technical Challenges & Critical Lessons
Building a metaphysical AI consulting system is not just about assembling AWS services; it is a battle for **Data Quality** and **Semantics**.

* **The Issue of Information Noise (RAG Hallucination):**
    * *Reality:* Metaphysical data is often abstract. When a user asks a vague question (e.g., "How is my future?"), Vector Search easily returns irrelevant results (Noise), causing the LLM to "hallucinate" and fabricate answers.
    * *Lesson:* The quality of the Knowledge Base is more important than quantity. **Chunking** data via `.jsonl` structure and meticulous **Metadata Tagging** are the keys to increasing Precision.
* **Language Challenges (Vietnamese Nuance):**
    * *Reality:* International Embedding models sometimes fail to fully grasp Sino-Vietnamese terms in Horoscopes (e.g., "Cung Mệnh", "Thiên Di").
    * *Solution:* Use of `cohere.embed-multilingual-v3` combined with **Hybrid Search** (pairing DynamoDB's exact keyword search with Pinecone's semantic search) to compensate for this deficiency.

### Future Roadmap
The next objective is not just for the AI to answer correctly, but to answer **"correctly specifically for YOU."** The system will shift from general consulting to identity-based consulting.

#### User Feedback Loop (RLHF Lite)
Implement a Like/Dislike mechanism for each response. This data will be recorded to:
1.  Refine Prompts (Prompt Tuning).
2.  Filter out low-quality RAG data segments from Pinecone.

---

### Closing
The current system has established a solid foundation: **Serverless** for cost optimization, **Pinecone** for memory processing, and **Python** to connect everything.

The shift towards **Personalization** will be a quantum leap, transforming the system from a "Search Engine" into a true **"Spiritual Companion,"** capable of deep understanding and companionship with the user.