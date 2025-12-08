---
title : "Data Strategy"
weight : 2
chapter : false
pre : " <b> 2.4.2. </b> "
---

This section required the most intensive research effort to ensure the quality of AI responses (*Garbage In, Garbage Out*).

## 1. Format Standardization Journey: From JSON to JSONL

- The choice of file storage format plays a decisive role in processing performance and AWS Lambda memory costs.
- After extensive testing, the system standardized the entire Knowledge Base (Tarot, Horoscope, Numerology) to the **JSONL (`.jsonl`)** format. In this format, each line of the file is a distinct, valid JSON object, independent of the others.

### Why JSONL?

* **Memory Optimization:** Utilizes **Lazy Loading** mechanisms instead of loading the entire file. This keeps Lambda RAM consumption low and stable, completely eliminating Out of Memory (**OOM**) errors with large datasets.
* **Local Fault Tolerance:** A syntax error in one line does not break the entire pipeline. The system automatically skips the erroneous line and continues processing, ensuring data flow continuity.
* **Streaming Compatibility:** Supports direct stream reading from S3, minimizing latency when initiating Batch processing.

### Comparison with Legacy Solution (Standard JSON)

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
![JSON](/images/json.png)
*Figure 2.1: Data sample in standard JSON format.*

* **JSONL (New - Line-delimited):**
    ```json
    {"id": 1, "text": "Aries..."}
    {"id": 2, "text": "Taurus..."}
    ```
![JSONL](/images/jsonl.png)
*Figure 2.2: Data configuration in JSONL format.*

## 2. "Divide and Conquer" Technique in Embedding

* **Challenge:** Raw text data is often too long and contains significant keyword noise. When RAG (Retrieval-Augmented Generation) queries entire large paragraphs, the AI's focus is easily "diluted," leading to rambling responses.
* **Solution:** Instead of embedding the entire text, the system implements:
    * *Flatten Contexts:* Breaks down information fields within contexts (e.g., `strengths`, `weaknesses`, `love`).
    * *Meta-Injection:* Attaches metadata (Name, Category, Keyword) to each micro-chunk before embedding.
* **Result:** During vector search, the system extracts the precise segment required (e.g., retrieving only the "Love aspects of Aries" segment rather than the entire Aries article), enabling the LLM to provide focused, accurate answers.

## 3. Database Design (DynamoDB)

To ensure sub-10ms latency for Real-time applications, the system utilizes **Amazon DynamoDB** with a dual-table design, serving two core purposes: Knowledge Storage and Conversation Context Storage.

![DynamoDB Tables](/images/dynamodb1.png)
*Figure 3.1: Overview of active DynamoDB tables.*

### MetaphysicalKnowledgeBase Table
This acts as the "Source of Truth" for the system, storing detailed information on Tarot, Zodiac signs, and Numerology. Data here serves as the Ground Truth for the RAG process.

* **Partition Key (PK):** `category` (String). E.g., `Tarot_card`, `Zodiac_sign`. Enables efficient querying by grouping large datasets by type.
* **Sort Key (SK):** `entity_name` (String). E.g., `Ace of Cups`, `Aries`. Unique identifier for each entity within a category.

**Item Structure Detail:**
Instead of storing flat text, the system stores structured JSON within the `contexts` attribute. This allows flexible extraction of specific aspects (Love, Career, Health) rather than retrieving the entire document.

![Knowledge Base Item](/images/dynamodb2.png)
*Figure 3.2: Attribute details for a Tarot card (Ace of Cups).*

| Attribute Name | Type | Description |
| :--- | :--- | :--- |
| `contexts` | String (JSON) | Contains categorized content segments (`general_upright`, `love_reversed`, etc.). This is the primary source for Embedding. |
| `keywords` | List | List of related keywords to support hybrid search alongside Semantic Search. |

### Chat History Table (sorcererxtreme-chatMessages)
To enable the AI to "remember" previous interactions (Context Retention), a short-term memory system is required. This table stores the entire conversation history per session.

* **Partition Key (PK):** `sessionId` (String). Unique identifier for the user's chat session.
* **Sort Key (SK):** `timestamp` (String). Message timestamp (ISO 8601), ensuring conversation logs are ordered chronologically.

![Chat History Item](/images/dynamodb3.png)
*Figure 3.3: Data sample of a stored user message.*

**Processing Flow:**
Whenever a user sends a new message:
1.  The system queries this table using the current `sessionId`.
2.  Retrieves the last $N$ messages (Context Window).
3.  Injects this history into the LLM Prompt to ensure coherent, context-aware responses.