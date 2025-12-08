---
title : "Storage & Retrieval"
weight : 3
chapter : false
pre : " <b> 2.4.3. </b> "
---

The system utilizes a **Hybrid Retrieval** mechanism (combining Vector Search and Key-Value Lookup). The core of this architecture is the use of Pinecone as the Long-term Memory for the AI.

## 1. Vector Database: The Power of Pinecone

Instead of self-managing infrastructure, the project selected **Pinecone**—a specialized Managed Vector Database. This component is crucial for enabling the AI to "understand" the semantics of Tarot or Horoscope inquiries, rather than relying solely on keyword matching.

### Index Configuration 
Based on the Serverless architecture, the system utilizes Pinecone's **Serverless Index** mode to optimize costs (paying only for data read/write, with no server maintenance fees).

![Pinecone Index Configuration](/images/pinecone1.jpg)
*Figure 3.1: Actual Index Configuration on Pinecone Console.*

**Key Technical Specifications:**
* **Dimensions:** **1024.** This vector length is sufficient to encode the complex semantic nuances of metaphysical texts while remaining more optimal than 4096-dimensional models (too heavy) or 768-dimensional models (potentially lacking detail).
* **Metric:** **Cosine Similarity.** The system uses Cosine to measure the angle between two vectors. In semantic space, the smaller the angle between two vectors (Cosine approaching 1), the more similar their meanings. This metric is best suited for NLP (Natural Language Processing) tasks compared to Euclidean distance.
* **Pod Type:** Serverless (Automatically scales based on demand, no pre-provisioning required).

### Record Structure
The true power of Pinecone lies in its ability to combine **Vector Search** with **Metadata Filtering**.

![Vector Record Structure](/images/pinecone1.jpg)
*Figure 3.2: Detail of a Vector Record including ID, Values, and Metadata.*

Each stored Record consists of three parts:
1.  **ID:** Unique identifier (Hashed from original content) to prevent data duplication (Dedup).
2.  **Values (Vector):** An array of 1024 floating-point numbers, representing the meaning of the text segment.
3.  **Metadata:** This is the most critical part for increasing Accuracy. Instead of searching the "entire ocean," the AI uses metadata to narrow the scope.
    * *Example:* When a user asks about "Leo's Love Life," the system filters by `category: "zodiac"` and `entity_name: "Leo"` first, before finding the nearest vector. This completely eliminates the possibility of the AI retrieving information for the wrong sign.

## 2. Trade-off Analysis: Why Pinecone over AWS RDS (pgvector)?

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

## 3. Why is DynamoDB still needed?
Although Pinecone is powerful for Semantic Search, the system still requires DynamoDB for:
* **Exact Match Retrieval:** Lambda Metaphysical needs to retrieve absolutely precise information based on IDs (e.g., the meaning of "The Fool" card when drawn, or specific star information in "Tu Vi"). DynamoDB performs this faster and cheaper than vector search.
* **Latency Reduction:** Offloads non-inferential tasks from the Vector DB.
* **Raw Dataset Storage:** Serves as a backup source and allows for quick metadata lookup without vector decoding.