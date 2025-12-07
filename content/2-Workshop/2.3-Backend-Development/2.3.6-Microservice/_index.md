---
title : "Connect microservices"
weight : 6
chapter : false
pre : " <b> 2.3.6 </b> "
---

In a Serverless architecture, when one Lambda function (e.g., `Chatbot`) needs to call another service (`MetaphysicalAPI`), the best approach is to use an HTTP request through that service's API Gateway.


### 1. Principles of API Calling

  * **Endpoint:** Always call through the public API Gateway Endpoint (or an internal one if both Lambdas are within a VPC and use a Private API Gateway).
  * **Library:** Use a familiar HTTP library like **`axios`** to manage the request and client-side timeout.
  * **Security:** Use IAM Roles or API Keys to authenticate calls between services if the API Gateway is not entirely public.


### 2. Timeout Management

Managing the **`timeout`** is the most critical factor to prevent 504 Gateway Timeout errors.

[Image of AWS API Gateway 29-second timeout limit diagram]

  * **API Gateway Limit:** The API Gateway has a **hard timeout limit of 29 seconds**. Any request lasting longer than 29 seconds is cut off and returns a 504 error.
  * **Lambda Limit:** Lambda can run up to 15 minutes, but it is constrained by the API Gateway. Therefore, the Lambda timeout must be set **less than 29 seconds** (e.g., 25 seconds).

| Configuration | Location | Recommended Value | Rationale |
| :--- | :--- | :--- | :--- |
| **API Gateway Timeout** | `serverless.yml` (Provider level) | 29 seconds | AWS maximum hard limit. |
| **Lambda Timeout (Receiver)** | `serverless.yml` (Function level) | 25 seconds | Must be less than 29s so Lambda can return an error before the API Gateway cuts the connection. |
| **Client Timeout (`axios`)** | TypeScript Code | 25,000 ms (25 seconds) | Ensures the client cuts the connection before Lambda times out, allowing for cleaner client-side error handling. |


### 3. Lambda Performance Optimization

If your AI Service performs heavy tasks (like RAG, astronomical calculations), optimizing processing speed is mandatory.

  * **Increase Memory (RAM):** Increase the `memorySize` of the AI Service Lambda to a higher level (e.g., **1536 MB** or **3008 MB**). On AWS, increasing RAM also increases CPU and Network Bandwidth, significantly speeding up heavy processing and reducing the likelihood of 504 errors.
  * **Code Configuration:** Configure `timeout: 25` in `serverless.yml` for both the Backend and the AI Service.

### 4. Code Example

Here is the AI calling function, modified to handle exceptions clearly and adhere to the Timeout principle.

```typescript
import axios from 'axios';

// Assume AI_SERVICE_URL is retrieved from AWS SSM Parameter Store and injected into ENV
const AI_SERVICE_URL = process.env.AI_SERVICE_URL; 
const CLIENT_TIMEOUT_MS = 25000; // 25 seconds (Below the 29s API Gateway limit)

/**
 * Calls the AI service's API Gateway to receive a response.
 * @param prompt The request data sent to the AI.
 */
export const callAI = async (prompt: string, userId: string) => {
  if (!AI_SERVICE_URL) {
    throw new Error("AI Service URL is not configured.");
  }
  
  try {
    const res = await axios.post(AI_SERVICE_URL, { 
      prompt,
      userId // Send user ID so the AI Service can log or handle VIPs
    }, {
      timeout: CLIENT_TIMEOUT_MS // Set client timeout
    });
    
    // Handle successful response
    return res.data; 

  } catch (error) {
    if (axios.isAxiosError(error) && error.code === 'ECONNABORTED') {
      // Client-side Timeout error
      console.error("AI Timeout Error: Request took too long.");
      throw new Error("AI Service timed out (504). Please try again.");
    }
    
    // Other errors (e.g., 4xx, 5xx from AI Service)
    console.error("AI Service Error:", error.message);
    throw new Error("AI Service unavailable or returned an error.");
  }
};
```