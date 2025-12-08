---
title : "AI Models & RAG Pipeline"
weight : 4
chapter : false
pre : " <b> 2.4.4. </b> "
---

The system leverages **Amazon Bedrock** as the centralized Unified API gateway, simplifying version management and credential security.

## 1. Model Selection Analysis

### Embedding: Cohere Embed Multilingual v3
* **ID:** `cohere.embed-multilingual-v3`
* **Why Cohere over Titan?**
    * **Vietnamese/Sino-Vietnamese Nuances:** Cohere v3 is trained on a massive multilingual dataset, allowing it to understand specific Sino-Vietnamese esoteric terms (e.g., "Thien Di", "Phu The") that Western-centric models often misinterpret.
    * **Input Type Parameter:** Supports `input_type="search_query"` (for user questions) and `"search_document"` (for database ingestion), optimizing the vector space for retrieval tasks.
    * **Dimension:** 1024 dimensions - The "sweet spot" balancing semantic accuracy and Pinecone storage costs.

### Generative LLM: Amazon Nova Pro
* **ID:** `amazon.nova-pro-v1:0`
* **Reasoning Capability:**
    * Unlike simple summarization models, Nova Pro excels at **logical chaining**. Example: It can synthesize the meanings of 3 separate Tarot cards (Past - Present - Future) into a coherent cause-and-effect narrative.
* **Inference Configuration:**
    * `temperature: 0.3`: Kept low to ensure the AI adheres strictly to the Knowledge Base, minimizing Hallucinations.
    * `topP: 0.9`: Allows slight stylistic flexibility to make the advice sound natural and empathetic.

## 2. AWS Lambda Implementation

Below is a Python (`boto3`) code snippet used within the AWS Lambda function to execute the RAG flow: Embed Query -> (Search Pinecone) -> Prompt Nova Pro.

```python
import boto3
import json
import os

# Initialize Bedrock Runtime client
bedrock = boto3.client(
    service_name='bedrock-runtime', 
    region_name='us-east-1'
)

def generate_embedding(text):
    """
    Step 1: Convert user question to Vector
    """
    response = bedrock.invoke_model(
        modelId='cohere.embed-multilingual-v3',
        contentType='application/json',
        accept='application/json',
        body=json.dumps({
            "texts": [text],
            "input_type": "search_query" # Optimized for queries
        })
    )
    result = json.loads(response['body'].read())
    return result['embeddings'][0]

def query_llm(context_chunk, user_question, history):
    """
    Step 2: Send Context + Question to Amazon Nova Pro
    """
    # System Prompt: Define the Persona
    system_prompt = """You are a dedicated Tarot and Astrology expert. 
    Answer the question based ONLY on the information provided in the <context> tags. 
    If the information is insufficient, honestly state that you do not know. Do not fabricate facts."""

    # Construct Message Payload (Nova Pro structure)
    prompt_payload = {
        "system": [{"text": system_prompt}],
        "messages": [
            # Chat history can be injected here
            {"role": "user", "content": [{"text": f"Context: {context_chunk}\n\nQuestion: {user_question}"}]}
        ],
        "inferenceConfig": {
            "temperature": 0.3, # Reduce hallucinations
            "maxTokens": 1000
        }
    }

    response = bedrock.invoke_model(
        modelId='amazon.nova-pro-v1:0',
        body=json.dumps(prompt_payload)
    )
    
    result = json.loads(response['body'].read())
    return result['output']['message']['content'][0]['text']