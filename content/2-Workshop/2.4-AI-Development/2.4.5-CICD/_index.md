---
title : "CI/CD & Testing"
weight : 5
chapter : false
pre : " <b> 2.4.5. </b> "
---

The system implements a fully automated CI/CD pipeline via **GitHub Actions**, ensuring that every line of code is rigorously tested (Test) before being deployed to the AWS Lambda environment.

## 1. CI Mechanism – Continuous Integration

Whenever new code is Pushed or a Pull Request is opened against the `main` branch, the CI workflow is automatically triggered to verify logic integrity.

**Workflow:**
`[Code Commit] ➔ [GitHub Actions Trigger] ➔ [Runner Ubuntu Start] ➔ [Setup Python Env] ➔ [Run Pytest] ➔ [Result: ✅ Pass / ❌ Fail]`

* **Mocking Strategy:** All Unit Tests utilize `unittest.mock` to simulate AWS services (S3, DynamoDB, Bedrock). This ensures:
    * **Zero Cost:** Testing does not incur AWS API fees.
    * **High Speed:** Tests complete in seconds rather than waiting for network latency.
    * **Safety:** Production data is never overwritten or deleted during testing.

## 2. CD Mechanism – Continuous Deployment

The CD pipeline is activated to package and deploy code to AWS only when the CI process returns a **Success (✅)** result.

**Workflow:**
`[CI Success] ➔ [Install Binary Libs] ➔ [Package Zip 📦] ➔ [AWS CLI Upload 🚀] ➔ [Lambda Live Update]`

**Note on Deployment Package:**
A major challenge with AWS Lambda is the compatibility of C-extension libraries (like `numpy` for calculations). The system resolves this by installing and building libraries with the `--platform manylinux2014_x86_64` flag directly within the CD pipeline to ensure absolute compatibility with the Amazon Linux environment.

## 3. Unit Testing Strategy

The testing framework covers three logic layers: **Input Validation**, **Happy Path**, and **Error Handling**.

### Chatbot Lambda (`sorcererxstreme-chatbot`)
* **Input Validation:**
    * *Case 1 (Missing Session):* Missing `sessionId` → Returns **400 Bad Request**.
    * *Case 2 (Missing Question):* Empty question content → Returns **400**.
    * *Case 3 (Invalid JSON):* Malformed JSON body → Returns **Parse Error**.
* **Happy Path:**
    * *Case 4:* Simulates full RAG flow: `Load History` → `Query Pinecone` → `Call Bedrock` → `Save Chat`. Asserts that AI returns a **200 OK** response.

### Embedding Lambda (`sorcererxstreme-embedding`)
* **Logic & Utilities:**
    * *Case 1 (Flatten Contexts):* Verifies JSON flattening. Input `{"hobbies": ["code", "read"]}` must become string `"hobbies: code, read"`.
    * *Case 2 (Bedrock Fail):* Simulates Bedrock network error. `get_embedding` must return `None` to avoid crashing the Batch process.
* **Integration Flow:**
    * *Case 3 (Happy Flow):* Simulates reading JSONL from S3 → Embedding 2 items → Writing to DynamoDB & Pinecone. Verifies API call counts match item counts.
    * *Case 4 (S3 Error):* Simulates S3 file not found → System reports **500 (Internal Error)**.

### Metaphysical Lambda (`sorcererxstreme-metaphysical`)
This is the most complex function with domain-based routing logic.

* **Input Validation:**
    * *Case 1 (Invalid Domain):* Sending `domain: "bitcoin"` → Returns **400** (Only Tarot, Horoscope, Numerology, Astrology supported).
    * *Case 2 (Missing Context):* Selecting "Horoscope" but missing `birth_date` → Returns input request.
    * *Case 3 (Empty Tarot):* Selecting "Tarot" but `cards_drawn` array is empty → Warns user to select cards.
* **Domain Logic:**
    * *Case 4 (Tarot Flow):* Mocks DynamoDB returning "The Sun" meaning. Verifies AI receives the correct context for analysis.
    * *Case 5 (Horoscope Flow):* Verifies integration with `lasotuvi` library. Mocks "Thien Ban" object to ensure AI receives "Element: Earth" (Mệnh Thổ) data.
    * *Case 6 (Graceful Degradation):* When Bedrock is throttled, the system returns a user-friendly message instead of crashing.