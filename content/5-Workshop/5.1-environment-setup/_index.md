---
title: "Environment Setup"
date: 2026-07-04
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

## Environment Setup

Before deploying anything to AWS, you need to install the required tools, configure credentials, clone the project, and install dependencies.

---

### Estimated deployment cost

Approximate monthly cost: ~$10.92/month.

Reminder: destroy the resources after testing to avoid ongoing charges.

---

### Required IAM Permissions

The deploying AWS identity needs minimum permissions for the services used in this workshop:

- CloudFormation
- CDK bootstrap and deployment
- Lambda
- API Gateway
- DynamoDB
- EventBridge
- SQS
- SNS
- CloudWatch
- IAM
- Cognito
- S3
- CloudFront
- WAF
- Secrets Manager

For a demo account, the simplest option is the `AdministratorAccess` managed policy. If you use a scoped policy, ensure it includes the actions needed to create and configure these resources.

---

### 5.1.1 Required Software

Install the following tools before proceeding.

#### Node.js 22 LTS

Download from [https://nodejs.org](https://nodejs.org).

```bash
node --version
# Expected: v22.x.x

npm --version
# Expected: 10.x.x
```

#### AWS CLI v2

Install from [https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html).

```bash
aws --version
# Expected: aws-cli/2.x.x
```

#### Git

```bash
git --version
# Expected: git version 2.x.x
```

---

### 5.1.2 AWS Account Setup

#### Configure credentials

```bash
aws configure
```

Enter the following:

```
AWS Access Key ID:     AKIAXXXXXXXXXXXXXXXX
AWS Secret Access Key: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
Default region name:   ap-southeast-1
Default output format: json
```

#### Verify identity

```bash
aws sts get-caller-identity
```

Expected:
```json
{
    "UserId": "AIDAXXXXXXXXXXXXXXXXX",
    "Account": "123456789012",
    "Arn": "arn:aws:iam::123456789012:user/your-username"
}
```

Note your **Account** number — you need it in the next step.

---

### 5.1.3 Clone and Install Dependencies

#### Clone the repository

```bash
git clone https://github.com/Hiep-123/ProjectAWS.git
cd ProjectAWS
```

> This repository URL is the GitHub remote shown by `git remote -v` for this project.

#### Install infrastructure dependencies

```bash
cd infrastructure
npm install
```

#### Verify CDK CLI

```bash
npx cdk --version
# Expected: 2.1126.0 (build xxxxxxx)
```

#### Install frontend dependencies

```bash
cd ../frontend
npm install
cd ../infrastructure
```

---

### 5.1.4 Configure Environment Variables

#### Create the infrastructure environment file

```bash
cp .env.example .env
```

Edit `infrastructure/.env` and fill in your values:

```bash
# ── AWS ──────────────────────────────────────────────────────────────
CDK_DEFAULT_ACCOUNT=123456789012       # your 12-digit AWS account ID
CDK_DEFAULT_REGION=ap-southeast-1

# ── Fill AFTER FrontendStack deploy ──────────────────────────────────
ALLOWED_ORIGIN=                         # https://dXXXX.cloudfront.net

# ── VNPay (used by ApiStack to create secret VNPayConfig) ────────────
VNP_TMN_CODE=                           # your VNPay merchant code
VNP_HASH_SECRET=                        # your VNPay hash secret
VNP_URL=https://sandbox.vnpayment.vn/paymentv2/vpcpay.html

# ── Alarm email ───────────────────────────────────────────────────────
ALERT_EMAIL=your@email.com

# ── Fill AFTER SecurityStack deploy ──────────────────────────────────
WAF_WEB_ACL_ARN=                        # arn:aws:wafv2:us-east-1:...

# ── Fill AFTER AuthStack deploy ───────────────────────────────────────
COGNITO_USER_POOL_ID=                   # ap-southeast-1_XXXXXXXXX

# ── Demo user credentials ─────────────────────────────────────────────
DEMO_CUSTOMER_EMAIL=customer@demo.com
DEMO_CUSTOMER_PASSWORD=Demo@Pass2024!  # min 8 chars, upper+lower+digit+symbol
```

#### Create the frontend environment file

```bash
cd ../frontend
cp .env.example .env
```

Edit `frontend/.env`:

```bash
VITE_API_URL=                           # fill after ApiStack deploy
VITE_COGNITO_USER_POOL_ID=              # fill after AuthStack deploy
VITE_COGNITO_CLIENT_ID=                 # fill after AuthStack deploy
VITE_COGNITO_REGION=ap-southeast-1
VITE_ENVIRONMENT=production
VITE_ENABLE_MOCKS=false
```

```bash
cd ../infrastructure
```

### 5.1.4.1 Prepare VNPay credentials (required for full checkout flow)

In the current architecture, `ApiStack` reads VNPay values from `infrastructure/.env` and uses them to create the Secrets Manager secret `VNPayConfig` automatically during deployment. The Lambda payment handler then reads the secret at runtime.

#### Values to set in `infrastructure/.env`

- `VNP_TMN_CODE`: your VNPay merchant code from the sandbox account.
- `VNP_HASH_SECRET`: the VNPay hash secret associated with that merchant.
- `VNP_URL`: the VNPay payment endpoint. For sandbox testing, use `https://sandbox.vnpayment.vn/paymentv2/vpcpay.html`.
- `ALLOWED_ORIGIN`: your deployed CloudFront origin, for example `https://d123abc.cloudfront.net`. The stack builds `VNP_RETURN_URL` as `${ALLOWED_ORIGIN}/payment/vnpay-return`.

Example:

```bash
VNP_TMN_CODE=YOUR_TMN_CODE
VNP_HASH_SECRET=YOUR_HASH_SECRET
VNP_URL=https://sandbox.vnpayment.vn/paymentv2/vpcpay.html
ALLOWED_ORIGIN=https://YOUR_CLOUDFRONT_DOMAIN
```

The secret created by the stack contains these JSON fields:

- `VNP_TMN_CODE`
- `VNP_HASH_SECRET`
- `VNP_URL`
- `VNP_RETURN_URL`

If you are only testing catalog, cart, and order flows, you can skip this step, but the VNPay checkout flow will not be fully functional.

#### Verify the secret after deployment

```bash
aws secretsmanager describe-secret --secret-id VNPayConfig --region ap-southeast-1
aws secretsmanager get-secret-value --secret-id VNPayConfig --region ap-southeast-1 --query SecretString --output text
```

You should see the four fields above in the secret payload.

#### Verify TypeScript compiles

```bash
npx tsc --noEmit --skipLibCheck
# Expected: no output — exit code 0
```

---

### 5.1.5 Bootstrap CDK

CDK Bootstrap creates the S3 bucket and IAM roles used to store deployment assets. Run once per account per region.

#### Bootstrap ap-southeast-1 (main region)

```bash
npx cdk bootstrap aws://123456789012/ap-southeast-1
```

> Replace `123456789012` with your 12-digit AWS account ID from `aws sts get-caller-identity`.

Expected:
```
Environment aws://123456789012/ap-southeast-1 bootstrapped.
```

#### Bootstrap us-east-1 (required for WAF WebACL)

```bash
npx cdk bootstrap aws://123456789012/us-east-1
```

> This value is the same AWS account ID used above; it is required because the WAF WebACL is deployed in us-east-1.

Expected:
```
Environment aws://123456789012/us-east-1 bootstrapped.
```

#### Verify

```bash
aws cloudformation describe-stacks \
  --stack-name CDKToolkit \
  --region ap-southeast-1 \
  --query "Stacks[0].StackStatus" \
  --output text
# Expected: CREATE_COMPLETE
```

---

### Troubleshooting

| Problem | Solution |
|---------|---------|
| `node: command not found` | Restart terminal after installation |
| `aws: command not found` | Restart terminal; verify AWS CLI added to PATH |
| `ExpiredTokenException` | Re-run `aws configure` with fresh credentials |
| `CDKToolkit in ROLLBACK_COMPLETE` | Delete the stack in CloudFormation console, then re-bootstrap |
| TypeScript errors on `tsc --noEmit` | Ensure `npm install` ran successfully in `infrastructure/` |
