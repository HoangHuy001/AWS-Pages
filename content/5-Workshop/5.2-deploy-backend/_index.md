---
title: "Deploy Backend Stacks"
date: 2026-07-04
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

## Deploy Backend Stacks

Deploy all backend CDK stacks in dependency order. Each stack exports CloudFormation values consumed by the next.

> **Prerequisite for the full checkout flow:** before deploying ApiStack, make sure the VNPay values are present in `infrastructure/.env`. The stack uses them to create or update the `VNPayConfig` secret in AWS Secrets Manager during deployment, and the payment Lambda reads it at runtime. Checkout will not work without these values. The return URL is derived from `ALLOWED_ORIGIN` and points to `/payment/vnpay-return`.

**Deployment order:**
```
Bootstrap → AuthStack → DatabaseStack → EventStack → ApiStack → SecurityStack → MonitoringStack → FrontendStack → InfrastructureStack → Seed Data → Verification → Cleanup
```

---

### 5.2.1 Deploy AuthStack

**Purpose:** Creates the Cognito User Pool, User Pool Client, and CUSTOMER/ADMIN groups. All protected API routes validate tokens issued by this pool.

```bash
cd infrastructure
npx cdk deploy AuthStack --require-approval never
```

Expected:
```
AuthStack

Outputs:
AuthStack.CognitoRegion = ap-southeast-1
AuthStack.UserPoolClientId = 1g8038olqqhg6psmka7etvc47e
AuthStack.UserPoolId = ap-southeast-1_dOmrwNnTw
```

**Post-deploy — update environment files:**

In `infrastructure/.env`:
```bash
COGNITO_USER_POOL_ID=ap-southeast-1_XXXXXXXXX
```
> This value is shown in the `AuthStack.UserPoolId` output above.

In `frontend/.env`:
```bash
VITE_COGNITO_USER_POOL_ID=ap-southeast-1_XXXXXXXXX
VITE_COGNITO_CLIENT_ID=xxxxxxxxxxxxxxxxxxxxxxxxxx
VITE_COGNITO_REGION=ap-southeast-1
```
> `VITE_COGNITO_USER_POOL_ID` comes from `AuthStack.UserPoolId`. `VITE_COGNITO_CLIENT_ID` comes from `AuthStack.UserPoolClientId`.

**Verify:**
```bash
aws cognito-idp describe-user-pool \
  --user-pool-id ap-southeast-1_XXXXXXXXX \
  --region ap-southeast-1 \
  --query "UserPool.{Name:Name,Status:Status}" \
  --output table
# Expected: Name=ecommerce-user-pool, Status=Enabled
```

---

### 5.2.2 Deploy DatabaseStack

**Purpose:** Creates `EcommerceTable` — a DynamoDB single-table design with three GSIs covering product catalog, user order history, and full product listing. All application data lives in this one table.

```bash
npx cdk deploy DatabaseStack --require-approval never
```

Expected:
```
DatabaseStack

Outputs:
DatabaseStack.TableArn = arn:aws:dynamodb:ap-southeast-1:123456789012:table/EcommerceTable
DatabaseStack.TableName = EcommerceTable
```

**Verify:**
```bash
aws dynamodb describe-table \
  --table-name EcommerceTable \
  --region ap-southeast-1 \
  --query "Table.{Status:TableStatus,Billing:BillingModeSummary.BillingMode,GSIs:GlobalSecondaryIndexes[*].IndexName}"
# Expected: Status=ACTIVE, Billing=PAY_PER_REQUEST, GSIs=[GSI1, GSI2, GSI3]
```

---

### 5.2.3 Deploy EventStack

**Purpose:** Creates the event-driven order processing pipeline: EventBridge custom bus, SQS OrderQueue (180s visibility, maxReceiveCount=3), SQS Dead Letter Queue, and the OrderProcessorFunction Lambda.

```bash
npx cdk deploy EventStack --require-approval never
```

This step takes ~2 minutes because CDK bundles the Lambda with esbuild.

Expected:
```
EventStack

Outputs:
EventStack.EventBusName = EcommerceEventBus
EventStack.OrderDLQName = EcommerceOrderDLQ
EventStack.OrderQueueName = EcommerceOrderQueue
EventStack.OrderProcessorFunctionName = OrderProcessorFunction
```
---

### 5.2.4 Deploy ApiStack

**Purpose:** Creates the REST API Gateway (100 rps / 200 burst) and three Lambda functions: ProductServiceFunction (public), CartServiceFunction (auth), and OrderServiceFunction (auth + EventBridge publisher).

```bash
npx cdk deploy ApiStack --require-approval never
```

Takes ~3 minutes (bundles three Lambda functions).

Expected:
```
ApiStack

Outputs:
ApiStack.ApiUrl = https://xxxxxxxxxx.execute-api.ap-southeast-1.amazonaws.com/prod/
```

**Post-deploy — update frontend/.env:**
```bash
VITE_API_URL=https://xxxxxxxxxx.execute-api.ap-southeast-1.amazonaws.com/prod/
```

---

### 5.2.5 Deploy SecurityStack

**Purpose:** Creates the WAF v2 WebACL in **us-east-1** with four rules: IP Reputation List, OWASP Common Rule Set, Known Bad Inputs (Log4j, Spring4Shell), and rate limiting at 1000 requests per 5 minutes per IP.

> **Note:** SecurityStack always deploys to us-east-1 regardless of `CDK_DEFAULT_REGION`. This is required for CloudFront-scoped WAF.

```bash
npx cdk deploy SecurityStack --require-approval never
```

Expected:
```
SecurityStack

Outputs:
SecurityStack.WafWebAclArn = arn:aws:wafv2:us-east-1:123456789012:global/webacl/EcommerceWebACL/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

**Post-deploy — update infrastructure/.env:**
```bash
WAF_WEB_ACL_ARN=arn:aws:wafv2:us-east-1:123456789012:global/webacl/EcommerceWebACL/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

**Verify:**
```bash
aws wafv2 list-web-acls \
  --scope CLOUDFRONT \
  --region us-east-1 \
  --query "WebACLs[?Name=='EcommerceWebACL'].Name" \
  --output text
# Expected: EcommerceWebACL
```

---

### 5.2.6 Deploy MonitoringStack

**Purpose:** Creates the CloudWatch Dashboard (5 rows covering all service tiers), six operational alarms, SNS topic, and email subscription for alarm notifications.

> **Prerequisite:** `ALERT_EMAIL` must be set in `infrastructure/.env`.

```bash
npx cdk deploy MonitoringStack --require-approval never
```

Expected:
```
MonitoringStack

Outputs:
MonitoringStack.AlarmsTopicArn = arn:aws:sns:ap-southeast-1:123456789012:EcommerceAlarmsTopic
MonitoringStack.DashboardName = EcommerceDashboard
```

**Confirm SNS subscription:**

AWS immediately sends a confirmation email to `ALERT_EMAIL`. Check your inbox for:

```
From: AWS Notifications <no-reply@sns.amazonaws.com>
Subject: AWS Notification - Subscription Confirmation
```

**Click the "Confirm subscription" link.** No alarm emails will be delivered until you do.

**Verify alarms:**
```bash
aws cloudwatch describe-alarms \
  --region ap-southeast-1 \
  --query "MetricAlarms[?starts_with(AlarmName,'Ecommerce')].{Alarm:AlarmName,State:StateValue}" \
  --output table
# Expected: 6 alarms all showing OK or INSUFFICIENT_DATA
```
or

**How to open the dashboard:**

![cloudwtach buoc 1](/images/5-Workshop/5.2-deploy-backend/cloudwatcha.jpg)
![cloudwtach buoc 2](/images/5-Workshop/5.2-deploy-backend/cloudwatchb.jpg)

This dashboard name is created by MonitoringStack.

---

### Troubleshooting

| Problem | Solution |
|---------|---------|
| `EcommerceEventBusArn is not an export` | EventStack not deployed yet — deploy in order |
| `EcommerceApiUrl is not an export` | ApiStack not deployed yet |
| Lambda bundling fails | Run `npm install` in `infrastructure/` to ensure esbuild is present |
| SecurityStack deploys to wrong account | Verify `CDK_DEFAULT_ACCOUNT` in `.env` is correct |
| No confirmation email after MonitoringStack | Check spam folder; verify `ALERT_EMAIL` has no typos |
