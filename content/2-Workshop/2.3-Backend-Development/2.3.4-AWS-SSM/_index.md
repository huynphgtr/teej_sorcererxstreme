---
title : "Storing Sensitive Environment Variables"
weight : 4 
chapter : false 
pre : " <b> 2.3.4 </b> "
---

Storing sensitive environment variables (`DATABASE_URL`, `JWT_SECRET`) in **AWS Systems Manager (SSM) Parameter Store** is the security standard for Serverless applications.

### 1. Preparation

In your `serverless.yml`, you used the variable `${self:provider.stage}` (defaulting to `dev` if not specified). To be consistent with that configuration, we should define the Key in the format **`/my-app/<stage>/<key>`**.

### 2. Run CLI Commands to Store Keys

You need to run these commands via the **AWS CLI** after configuring access permissions.

```bash
# NOTE: Replace <YOUR_VALUE> with your actual value.
# We will use the default stage "dev" for the workshop environment.

# --- 1. Store NeonDB Connection String ---
# Use "SecureString" to encrypt the data.
aws ssm put-parameter \
  --name "/my-app/dev/database_url" \
  --value "postgresql://user:pass@ep-xyz.aws.neon.tech/neondb?sslmode=require" \
  --type "SecureString" \
  --overwrite

# --- 2. Store JWT Secret ---
aws ssm put-parameter \
  --name "/my-app/dev/jwt_secret" \
  --value "a_very_long_and_secure_jwt_key_314159" \
  --type "SecureString" \
  --overwrite

# --- 3. Store Frontend URL (If necessary for CORS) ---
aws ssm put-parameter \
  --name "/my-app/dev/frontend_url" \
  --value "https://your-amplify-app.amplifyapp.com" \
  --type "String" \
  --overwrite
```

### 3. Verification

After storing, you can run the following command to check if the Parameter has been saved correctly and if the Serverless Framework can access it:

```bash
# Command to check and decrypt the value (requires SSM Read permission)
aws ssm get-parameter \
  --name "/my-app/dev/database_url" \
  --with-decryption
```

> **Important Note:** In `serverless.yml`, you defined:
> `DATABASE_URL: ${ssm:/my-app/prod/database_url}`.
> To synchronize with the command above, you need to change `prod` to **`dev`** in `serverless.yml` if you are deploying with the default stage, or run the CLI commands with the `prod` stage if you wish to keep the `serverless.yml` file as is.