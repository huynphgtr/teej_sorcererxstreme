---
title : "Configure Serverless Framework"
weight : 3
chapter : false
pre : " <b> 2.3.3 </b> "
---

This is the most crucial step, where we define the AWS infrastructure (IaC) and optimize the deployment package for Lambda.

### 1. The `serverless.yml` File

This configuration file includes sections for SSM Security, Build/Package Optimization, and necessary IAM Roles.

```yaml
# serverless.yml
org: your_organization_name
service: my-serverless-backend # Ensure service name matches the directory

provider:
  name: aws
  runtime: nodejs20.x
  region: ap-southeast-1
  timeout: 29 # Maximum allowed by API Gateway (keep as is)
  memorySize: 512 # Lambda memory configuration (recommended for Prisma)

  # --- VPC Network Configuration (MANDATORY for NeonDB) ---
  # If NeonDB requires a fixed IP or you use an internal RDS,
  # Lambda needs VPC configuration to connect externally or within the VPC.
  # (Assumed for NeonDB Public Access, but VPC needed if using Private Subnet)
  # vpc:
  #   securityGroupIds: [sg-xxxxxxxx]
  #   subnetIds: [subnet-xxxxxx]

  # --- Environment Variables (Fetched from SSM) ---
  environment:
    # Retrieve secrets from AWS SSM Parameter Store (Encrypted)
    DATABASE_URL: ${ssm:/my-app/${self:provider.stage}/database_url}
    JWT_SECRET: ${ssm:/my-app/${self:provider.stage}/jwt_secret}
    FRONTEND_URL: ${ssm:/my-app/${self:provider.stage}/frontend_url, 'http://localhost:3000'} # Add local fallback
    PRISMA_CLI_BINARY_TARGETS: rhel-openssl-3.0.x # Crucial for Prisma

  # --- IAM Permissions (Required) ---
  # Add necessary permissions for Lambda to read SSM and access other services
  iam:
    role:
      statements:
        - Effect: 'Allow'
          Action:
            - 'ssm:GetParameter'
          Resource: 'arn:aws:ssm:${self:provider.region}:*:parameter/my-app/${self:provider.stage}/*'

  # --- DDoS/Billing Protection (Keep as is) ---
  apiGateway:
    usagePlan:
      quota:
        limit: 5000000
        period: MONTH
      throttle:
        burstLimit: 200
        rateLimit: 100

# --- Deployment Package Size Optimization ---
build:
  esbuild:
    bundle: true
    minify: true
    sourcemap: false
    # Explicitly specify modules to exclude from the main package
    external:
      - 'aws-sdk'
      - '@prisma/client/runtime/library'

package:
  individually: true
  patterns:
    - 'src/handler.js'
    - 'src/app.js'
    - 'src/**/*.js'
    - 'dist/**/*.js' # Ensure compiled JS files are packaged
    - 'package.json'
    - 'node_modules/**'
    # --- Define Prisma Binary Files (Extremely important) ---
    # Only include the necessary Linux binary file to keep package < 250MB
    - 'node_modules/.prisma/client/libquery_engine-rhel-openssl-3.0.x.so.node'
    - 'node_modules/.prisma/client/schema.prisma'
    - '!./**' # Remove all unnecessary files after defining required patterns above
    - '!node_modules/aws-sdk/**' # Reduce size by excluding SDK already available in Lambda

plugins:
  - serverless-offline
  - serverless-dotenv-plugin 

functions:
  api:
    handler: src/handler.handler
    events:
      - http: { path: /, method: ANY }
      - http: { path: /{proxy+}, method: ANY }
```

### 2. Required Steps

#### A. Store Secrets in AWS SSM (Security)

Before deployment, you must store sensitive values in the AWS SSM Parameter Store so the Serverless Framework can read them.

```bash
# Command to store DATABASE_URL (SecureString)
aws ssm put-parameter \
  --name "/my-app/dev/database_url" \
  --value "postgresql://user:password@endpoint..." \
  --type "SecureString" \
  --overwrite

# Repeat for JWT_SECRET and FRONTEND_URL
```

#### B. Run Local Testing

Use the `serverless-offline` plugin to run the API locally, connecting directly to NeonDB via the `.env` file.

```bash
# Run local API on port 3000 (default)
sls offline start
```

#### C. First-time Deployment

Once the code has been locally tested, you are ready to deploy the entire infrastructure to AWS.

```bash
# Deploy all resources (Lambda, API Gateway, IAM)
sls deploy 
```