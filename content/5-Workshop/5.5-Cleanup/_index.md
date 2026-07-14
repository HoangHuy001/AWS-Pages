---
title: "Cleanup"
date: 2026-07-04
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

## Cleanup

Delete all AWS resources created during this workshop to stop ongoing charges.

> **Warning:** This is **irreversible**. All data including DynamoDB items, Cognito users, CloudWatch logs, and S3 assets will be permanently deleted. Do not run cleanup if you want to keep the deployed application.

---

### 5.6.1 Destroy All Stacks

Run from the `infrastructure/` directory:

```bash
cd infrastructure
npx cdk destroy --all --force
```

CDK will destroy each stack in reverse dependency order automatically.

Expected output per stack:
```
FrontendStack: destroying...
FrontendStack: destroyed

InfrastructureStack: destroying...
InfrastructureStack: destroyed

MonitoringStack: destroying...
MonitoringStack: destroyed

SecurityStack: destroying...
SecurityStack: destroyed

ApiStack: destroying...
ApiStack: destroyed

EventStack: destroying...
EventStack: destroyed

DatabaseStack: destroying...
DatabaseStack: destroyed

AuthStack: destroying...
AuthStack: destroyed
```

> **Note:** CloudFront distributions take 5–10 minutes to fully disable before deletion. If FrontendStack gets stuck, wait and retry:
> ```bash
> npx cdk destroy FrontendStack --force
> ```

---

### 5.6.2 Verify All Stacks Deleted

```bash
# ap-southeast-1 stacks
aws cloudformation list-stacks \
  --region ap-southeast-1 \
  --stack-status-filter DELETE_COMPLETE \
  --query "StackSummaries[?contains(['AuthStack','DatabaseStack','EventStack','ApiStack','MonitoringStack','FrontendStack','InfrastructureStack'],StackName)].{Stack:StackName,Status:StackStatus}" \
  --output table
```

```bash
# us-east-1 — SecurityStack
aws cloudformation list-stacks \
  --region us-east-1 \
  --stack-status-filter DELETE_COMPLETE \
  --query "StackSummaries[?StackName=='SecurityStack'].{Stack:StackName,Status:StackStatus}" \
  --output table
```

Both queries should show all stacks with `DELETE_COMPLETE`.

---

### 5.6.3 Verify DynamoDB Table Deleted

```bash
aws dynamodb describe-table \
  --table-name EcommerceTable \
  --region ap-southeast-1 2>&1 | grep -i "error\|not found\|ResourceNotFoundException"
# Expected: ResourceNotFoundException
```

---

### 5.6.4 Verify S3 Bucket Deleted

```bash
aws s3 ls | grep ecommerce-frontend
# Expected: no output (bucket deleted)
```

If the bucket still exists (CDK failed to empty it):

```bash
# Empty the bucket first
aws s3 rm s3://ecommerce-frontend-123456789012-ap-southeast-1 --recursive

# Replace 123456789012 with your AWS account ID from the earlier `aws sts get-caller-identity` output.

# Then retry destroying FrontendStack
npx cdk destroy FrontendStack --force
```

---

### 5.6.5 Clean Local Build Artifacts

```bash
cd infrastructure && rm -rf dist/ cdk.out/
cd ../frontend && rm -rf dist/
```

---

### What Was Deleted

| Resource | Stack |
|----------|-------|
| Cognito User Pool + users | AuthStack |
| DynamoDB EcommerceTable + all data | DatabaseStack |
| EventBridge bus + rule | EventStack |
| SQS OrderQueue + DLQ | EventStack |
| Lambda × 4 (Products, Cart, Orders, Processor) | ApiStack + EventStack |
| API Gateway REST API | ApiStack |
| CloudWatch log groups × 5 | ApiStack + EventStack |
| WAF WebACL (us-east-1) | SecurityStack |
| CloudWatch Dashboard + 6 alarms | MonitoringStack |
| SNS topic + email subscription | MonitoringStack |
| S3 frontend bucket | FrontendStack |
| CloudFront distribution | FrontendStack |
| CDK Bootstrap resources | (retained — safe to keep for future use) |

---

### Troubleshooting

| Problem | Solution |
|---------|---------|
| CloudFront distribution stuck deleting | Wait 5–10 min; run `npx cdk destroy FrontendStack --force` again |
| S3 bucket not empty error | Run `aws s3 rm s3://ecommerce-frontend-ACCOUNT-ap-southeast-1 --recursive` then retry |
| Stack stuck in `DELETE_IN_PROGRESS` | Check CloudFormation console Events tab for the blocking resource; wait and retry |
| SecurityStack not deleted | Run `npx cdk destroy SecurityStack --force` (targets us-east-1 internally) |
| DynamoDB table retained | The table has `RemovalPolicy.DESTROY` — if it persists, delete it manually in the DynamoDB console |

---

### Congratulations

You have successfully completed the workshop. You deployed a full production-grade AWS Serverless E-commerce Platform including:

- Cognito authentication with JWT
- DynamoDB single-table design with 3 GSIs
- Event-driven order processing (EventBridge → SQS → Lambda)
- REST API with Cognito authorizer
- WAF protection (OWASP CRS + IP reputation + rate limiting)
- CloudFront CDN with OAC and security headers
- CloudWatch observability (Dashboard + 6 alarms + SNS email)
- React 19 SPA with runtime config injection
