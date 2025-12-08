---
title : "Lessons & Roadmap"
weight : 6
chapter : false
pre : " <b> 2.4.6. </b> "
---

## 1. Technical Challenges & Critical Lessons
Building a metaphysical AI consulting system is not just about assembling AWS services; it is a battle for **Data Quality** and **Semantics**.

* **The Issue of Information Noise (RAG Hallucination):**
    * *Reality:* Metaphysical data is often abstract. When a user asks a vague question (e.g., "How is my future?"), Vector Search easily returns irrelevant results (Noise), causing the LLM to "hallucinate" and fabricate answers.
    * *Lesson:* The quality of the Knowledge Base is more important than quantity. **Chunking** data via `.jsonl` structure and meticulous **Metadata Tagging** are the keys to increasing Precision.
* **Language Challenges (Vietnamese Nuance):**
    * *Reality:* International Embedding models sometimes fail to fully grasp Sino-Vietnamese terms in Horoscopes (e.g., "Cung Mệnh", "Thiên Di").
    * *Solution:* Use of `cohere.embed-multilingual-v3` combined with **Hybrid Search** (pairing DynamoDB's exact keyword search with Pinecone's semantic search) to compensate for this deficiency.

## 2. Future Roadmap
The next objective is not just for the AI to answer correctly, but to answer **"correctly specifically for YOU."** The system will shift from general consulting to identity-based consulting.

### User Feedback Loop (RLHF Lite)
Implement a Like/Dislike mechanism for each response. This data will be recorded to:
1.  Refine Prompts (Prompt Tuning).
2.  Filter out low-quality RAG data segments from Pinecone.

---

## 3. Closing
The current system has established a solid foundation: **Serverless** for cost optimization, **Pinecone** for memory processing, and **Python** to connect everything.

The shift towards **Personalization** will be a quantum leap, transforming the system from a "Search Engine" into a true **"Spiritual Companion,"** capable of deep understanding and companionship with the user.