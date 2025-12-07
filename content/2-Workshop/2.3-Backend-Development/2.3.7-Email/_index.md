---
title : "Setup Email"
weight : 7
chapter : false
pre : " <b> 2.3.7 </b> "
---

To ensure the highest delivery reliability for project emails and prevent them from being marked as Spam by email service providers (like Gmail, Outlook), domain security configuration is mandatory.

### 1. Basic Configuration and Identity Security

| Step | Action | Purpose |
| :---: | :--- | :--- |
| **1. Domain Purchase** | Use a private domain (`.com`, `.xyz`) instead of a shared one. | **Reputation:** Establish a separate email sending reputation, avoiding impact from other bad senders. |
| **2. Verify Domain in AWS SES** | Access SES -\> Verified Identities -\> Create Identity (Select Domain). | **Proof of Ownership:** Allows AWS SES to manage email sending on behalf of your domain. |
| **3. Configure DNS (Email Security)** | Add CNAME (DKIM), TXT (SPF), and TXT (DMARC) records. | **Crucial:** These records verify the sending source (DKIM/SPF) and instruct the receiving server how to handle invalid emails (DMARC), preventing emails from falling into Spam. |

#### Details of Mandatory DNS Records:

  * **DKIM (CNAME):** Add the **3 CNAME records** provided by AWS SES. *Purpose: Digital Signature.*
  * **SPF (TXT):** Add a `TXT` record with the content: `v=spf1 include:amazonses.com ~all`. *Purpose: Designates AWS SES as an authorized server to send mail.*
  * **DMARC (TXT):** Add a `TXT` record (usually as `_dmarc`) with the content: `v=DMARC1; p=none;`. *Purpose: Establishes reporting and handling policies for fraudulent emails.*


### 2. Setting up the Sending Flow and Exiting Sandbox

After the domain has been verified (**Verification Status: Verified**), you need to set up the operational flow and request actual sending permission.

| Step | Interacting Service | Action |
| :---: | :--- | :--- |
| **1. Activate Async Flow** | EventBridge Scheduler -\> Lambda (TriggerReminder) | The asynchronous flow starts according to a defined schedule. |
| **2. Send SES Request** | Lambda (TriggerReminder) -\> Amazon SES API | The Lambda function (with the granted IAM Role) directly calls the SES API (e.g., `SendEmailCommand`) to send personalized emails to each user. |
| **3. Request Production Access** | AWS Support Center | Submit a ticket requesting AWS to upgrade your account from Sandbox mode so you can send emails to any unverified address. |