---
title : "AI Development"
weight : 4
chapter : false
pre : " <b> 2.4. </b> "
---

## 1. Introduction

This document serves as a Technical Case Study detailing the process of building a multi-domain AI consulting system (Tarot, Numerology, Horoscopes, Astrology) based on Serverless architecture. The core objective is to solve the AI "hallucination" problem through a rigorous RAG (Retrieval-Augmented Generation) pipeline, ensuring responses strictly adhere to the verified Knowledge Base.

### Problem Statement: AI Challenges in Esoteric Domains

Developing AI for the esoteric domain presents unique technical barriers that standard chatbot solutions often fail to address:

* **Abstract Nature and Ambiguity:** The data contains numerous Sino-Vietnamese terms and abstract concepts (e.g., card meanings changing based on spread positions, planetary influences shifting by birth hour). General-purpose LLMs often struggle to grasp this narrow context.
* **Requirement for Absolute Accuracy (Zero Hallucination):** Users seeking advice demand precision. AI fabricating knowledge (such as incorrect star positions or wrong number interpretations) is a critical risk that must be eliminated.
* **Unstructured Data Processing:** The input Knowledge Base is heterogeneous, requiring a complex ETL process to standardize data into Vector Embeddings without losing original semantic meaning.

### Cost Optimization Strategy

A top priority for this project is optimizing operating expenses (OpEx) from Day 1. Instead of maintaining expensive server clusters 24/7, the system fully adopts a **Pay-as-you-go** model.

This architecture minimizes financial risk during the MVP (Minimum Viable Product) phase when user traffic is unpredictable. Below is the Unit Economics breakdown for key services:

| Service | Role | Pricing Model | Est. Unit Cost |
| :--- | :--- | :--- | :--- |
| AWS Lambda | Backend Logic | Per request & compute duration | ~$0.20 / 1M requests |
| Pinecone Serverless | Vector Database (Storage) | Per Storage & Read Units | ~$0.33 / GB / month |
| Amazon Bedrock | LLM Inference (Nova Pro) | Per Input/Output Tokens | Input: ~$0.0008 / 1k tokens<br>Output: ~$0.0032 / 1k tokens |
| Cohere Embed | Embedding Model (Multilingual) | Per tokens processed | ~$0.10 / 1M tokens |
| DynamoDB | Metadata & Cache | Write/Read Capacity Units (On-demand) | ~$1.25 / 1M Write Units |

> **Assessment:** With this architecture, the system maintenance cost during idle states is virtually zero.

## 2. AI Architecture

![AI-Architecture](/images/AI-architecture.png)

## Table of Contents

1.  [System Architecture](2.4.1-Architecture/)
2.  [Data Strategy](2.4.2-Data-strategy/)
3.  [Storage & Retrieval](2.4.3-Storage-retrieval/)
4.  [AI Models & RAG](2.4.4-Models-rag/)
5.  [CI/CD & Testing](2.4.5-CICD/)
6.  [Lessons & Roadmap](2.4.6-Roadmap/)