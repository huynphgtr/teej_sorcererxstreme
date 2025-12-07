---
title : "Backend Development"
weight : 1 
chapter : false
pre : " <b> 2.3. </b> "
---
## Building and Automating with Serverless Backend (Serverless V4)

This guide is a comprehensive document detailing the entire Backend development process for the **SorcererXtreme AI** project, from setting up the local development environment to completing the automated deployment workflow (CI/CD) on AWS.


### 1. Context & Technical Challenges

In the development of high-performance Serverless applications, we face several complex technical hurdles:

* **Framework Transition:** The transition from a traditional HTTP Framework like **Express.js** to the **stateless** environment of **AWS Lambda** requires using `serverless-http` and a change in source code structure.
* **Database Management:** Using a sophisticated ORM like **Prisma** on a Serverless platform necessitates techniques to optimize the deployment package size to under 250MB and requires downloading the correct **Prisma Binary (rhel-openssl-3.0.x)** for the Lambda Linux environment. 
* **Security:** Ensuring that sensitive connection strings are **never** stored in the source code, but are securely managed using **AWS SSM Parameter Store**.


### 2. Core Value of the Constructed Architecture

Our Backend architecture is designed to overcome these challenges, delivering key benefits:

* **Cost and Performance Optimization:** Utilizing **AWS Lambda** and **Serverless Framework V4** ensures you only pay for the time your code actually runs. Optimizing the Prisma package significantly **reduces Cold Start time**.
* **Full Automation (CI/CD):** Building the **GitHub Actions** workflow automates the entire development loop: Code -> Push -> Build -> Deploy, completely eliminating manual deployment steps and minimizing human error. 
* **Data Flexibility:** Establishing a stable and secure connection to an **external Database** (NeonDB) demonstrates the capability for flexible integration in real-world projects.

This guide will serve as a detailed, step-by-step map to help you master the entire process of modern Serverless Backend development.


1. [Prepare](2.3.1-Prepare/)
2. [Set up environment](.3.2-Set-up/)
3. [Configure Serverless Framework](2.3.3-Configure-serverless/)
4. [Save secret key](2.3.4-AWS-SSM/)
5. [Automation CI/CD](2.3.5-CICD/)
6. [Connect microservices](2.3.6-Microservice/)
7. [Set up email](2.3.7-Email/)
8. [Development workflow](2.3.8-Summary/)
