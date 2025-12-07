---
title: "Cleanup Resources"
weight: 7
chapter: false
pre: " <b> 2.2.7. </b> "
---


After completing the Workshop, cleaning up is **mandatory** to avoid unexpected AWS charges. Since we use Serverless Services (Amplify, Lambda, Bedrock), cleanup is straightforward but requires attention.

### 1. Delete AWS Amplify App

This is the most critical step. Deleting the App on Amplify automatically cleans up 80% of related resources (S3 Hosting, CloudFront, CI/CD Pipeline).

1.  Access **AWS Amplify Console**.
2.  Select the **SorcererXtreme** app.
3.  Click **Actions** tab (top right) -> Select **Delete app**.
4.  Type the confirmation phrase (usually `delete`) and click Confirm.

### 2. Cleanup Database & External Services

Since **NeonDB** and **Pinecone** are 3rd party services (not included in Amplify Delete), you must delete them manually:

*   **NeonDB:**
    *   Log in to Neon Console.
    *   Go to Project **Settings** -> **Delete Project**.
*   **Pinecone:**
    *   Log in to Pinecone Console.
    *   Delete the **Index** (e.g., `tarot-knowledge-base`) to stop vector storage billing.

### 3. Manual AWS Resource Cleanup (If created separately)

If you created extra resources outside of Amplify during the workshop, check:

*   **Amazon Bedrock:** Bedrock is billed On-demand per request, so you don't need to "delete" Models. However, if you created a custom **Knowledge Base**, delete it.
*   **Amazon Cognito:** Check if the User Pool is gone (Amplify usually deletes it).
*   **Parameter Store:** Go to AWS Systems Manager > Parameter Store > Delete keys like `/sorcerer/neon_db_url`, `/sorcerer/pinecone_api_key`.

### 4. Final Switch (Billing Dashboard)

To be 100% sure:
1.  Access **AWS Billing Dashboard**.
2.  Check the **"Bills"** section.
3.  Wait 24h for the system to update and ensure no new charges are appearing from unknown services.

---

### Conclusion

Congratulations on reaching the end of the journey! 

You have successfully built **SorcererXtreme** - an application blending Frontend artistry (Next.js), Cloud power (AWS Amplify), and Artificial Intelligence (Bedrock RAG).


See you in advanced Workshops! 
