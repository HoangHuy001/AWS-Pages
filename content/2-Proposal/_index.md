---
title: "Proposal"
date: 2026-07-04
weight: 2
chapter: false
pre: "2. "
---

# AWS Serverless E-commerce Platform

> A production-ready cloud-native shopping system built on AWS Serverless Architecture

## 1. Executive Summary

This project delivers a fully functional, production-grade e-commerce platform built entirely on AWS serverless and managed services. The system supports the complete customer shopping lifecycle — product browsing, cart management, order placement, and real-time order tracking — with no server infrastructure to manage or maintain.

The platform is deployed to the **ap-southeast-1 (Singapore)** region using **AWS Cloud Development Kit (CDK) with TypeScript** as the infrastructure-as-code framework. It demonstrates applied knowledge of AWS Well-Architected principles across security, reliability, performance, cost optimization, and operational excellence.

The project was developed and delivered during a one-month internship as a practical demonstration of cloud architecture design, serverless development, and production deployment on AWS.


## 2. Problem Statement

#### Background

Modern e-commerce businesses require infrastructure that can handle unpredictable traffic, maintain high availability, enforce security at every layer, and remain cost-efficient during periods of low activity. Traditional server-based deployments carry significant operational overhead: servers must be provisioned, patched, scaled, and monitored continuously regardless of actual traffic.

#### Key Challenges

**Operational complexity:** Server-based systems require dedicated operations teams, scheduled maintenance windows, and manual scaling decisions that introduce risk and cost.

**Cost inefficiency at variable load:** Fixed-capacity servers incur costs even when idle. An e-commerce platform naturally has periods of very low traffic (overnight, off-season) where idle capacity is wasted.

**Security surface area:** Publicly exposed servers, unencrypted data at rest and in transit, and weak authentication mechanisms remain the leading causes of e-commerce data breaches.

**Developer velocity:** Tightly coupled monolithic backends slow down iteration and make it difficult to deploy improvements to individual service areas without risking the entire system.

#### The Opportunity

AWS provides a fully managed serverless ecosystem that addresses all of these challenges. By combining API Gateway, Lambda, DynamoDB, EventBridge, and CloudFront, it is possible to build a scalable, secure, observable e-commerce backend that costs nothing when idle and scales automatically under load — without managing a single server.


## 3. Objectives

1. Design and implement a production-ready serverless e-commerce API covering products, cart, and orders.
2. Implement event-driven asynchronous order processing using EventBridge and SQS to decouple order creation from order fulfillment.
3. Enforce authentication and authorization using Amazon Cognito with JWT token verification at the API layer.
4. Deploy the complete infrastructure as code using AWS CDK TypeScript, ensuring repeatable and auditable deployments.
5. Secure the customer-facing application with AWS WAF, CloudFront, TLS 1.2+, and least-privilege IAM.
6. Achieve full observability with a CloudWatch Dashboard, six operational alarms, X-Ray tracing, and SNS-based email alerting.
7. Deliver a responsive React 19 single-page application served via CloudFront with content-hashed asset caching.
8. Demonstrate alignment with all six pillars of the AWS Well-Architected Framework.


## 4. Proposed Solution

The solution is a cloud-native e-commerce platform composed of independently deployable infrastructure stacks, each responsible for a clearly defined domain.

#### Architecture Philosophy

The system follows a **serverless-first, event-driven** design pattern. No EC2 instances, no containers, and no persistent server processes are used. Every compute unit is a Lambda function invoked on demand. Every infrastructure resource is defined in TypeScript using AWS CDK and deployed through CloudFormation.

#### Customer Flow

A customer interacts with a React 19 single-page application served from CloudFront. API calls travel from the browser through CloudFront to API Gateway, which validates the Cognito JWT and routes the request to the appropriate Lambda function. Lambda reads from and writes to DynamoDB using a single-table design. When a customer places an order, the Order Service publishes an `OrderCreated` event to EventBridge, which routes it to an SQS queue. The Order Processor Lambda consumes the queue message asynchronously, updates the order status through the `PENDING → PROCESSING → COMPLETED` lifecycle, and publishes status events back to EventBridge. The customer's browser polls the order detail endpoint every three seconds to display live status updates.

#### Key Design Decisions

- **Single-table DynamoDB design** — all entities (products, cart items, orders) share one table with GSI-based access patterns, eliminating the need for joins and enabling O(1) key-based reads for all hot paths.
- **HTTP 202 Accepted for order creation** — the Order Service returns immediately after persisting the PENDING order and publishing the event, keeping the API response time deterministic regardless of downstream processing complexity.
- **Runtime configuration injection** — Cognito pool IDs and the API URL are injected at deploy time via a `/config.json` file served from CloudFront, preventing environment-specific values from being baked into the JavaScript bundle.
- **WAF in us-east-1** — the WAF WebACL is deployed in us-east-1 as required for CloudFront-scoped web ACLs, and its ARN is passed to the FrontendStack as an environment variable to work around the cross-region limitation of CloudFormation exports.


## 5. AWS Architecture Overview

{{< img src="images/2-Proposal/diagram.png" alt="AWS Serverless Architecture" >}}


## 6. AWS Services

| Service | Role in the Platform |
|---------|---------------------|
| **Amazon Cognito** | User registration and authentication. Email-based sign-up with verification, SRP and USER_PASSWORD auth flows, CUSTOMER and ADMIN groups, and JWT tokens for every protected API request. |
| **Amazon API Gateway** | REST API entry point with throttling, Cognito JWT authorizer, structured logging, and public/protected routes for catalog, cart, orders, and payments. |
| **AWS Lambda** | The current system uses five Lambda functions: ProductServiceFunction, CartServiceFunction, OrderServiceFunction, PaymentServiceFunction, and OrderProcessorFunction. They run on ARM64 Graviton2 (Node.js 22), are bundled with esbuild, and use active X-Ray tracing. |
| **Amazon DynamoDB** | Single-table design with PAY_PER_REQUEST billing, PITR, TTL for cart expiry, and GSIs for catalog, order history, and user access patterns. |
| **Amazon EventBridge** | Custom event bus `EcommerceEventBus` is used to publish `OrderCreated` events and drive asynchronous order processing. |
| **Amazon SQS** | `EcommerceOrderQueue` and `EcommerceOrderDLQ` provide reliable asynchronous processing, retry handling, and dead-letter isolation for failed order workflows. |
| **Amazon Secrets Manager** | Stores VNPay credentials for the PaymentServiceFunction so sensitive configuration is not hardcoded in source code. |
| **Amazon S3** | Private bucket hosting the React SPA build. It is encrypted, versioned, HTTPS-only, and only accessible through CloudFront via OAC. |
| **Amazon CloudFront** | Global CDN serving the frontend with OAC, TLS 1.2+, HTTP/2 and HTTP/3, SPA routing fallback, and cache policies for the Vite build output. |
| **AWS WAF v2** | WebACL attached to CloudFront in us-east-1 for IP reputation filtering, OWASP attack rule sets, known-bad-input detection, and rate limiting. |
| **Amazon CloudWatch** | Operational dashboard, alarms, and log aggregation for API Gateway, Lambda, EventBridge, and SQS metrics. |
| **Amazon SNS** | `EcommerceAlarmsTopic` receives CloudWatch alarm notifications for the operations email channel. |
| **AWS CDK (TypeScript)** | All infrastructure is defined as code in eight CDK stacks: AuthStack, DatabaseStack, EventStack, ApiStack, MonitoringStack, SecurityStack, FrontendStack, and InfrastructureStack. |
| **AWS X-Ray** | Active tracing is enabled across Lambda functions to inspect end-to-end calls from API requests into DynamoDB and EventBridge. |


## 7. Well-Architected Considerations

#### Operational Excellence
All infrastructure is defined as code in AWS CDK TypeScript. Every stack is independently deployable with `npx cdk deploy <StackName>`. CloudWatch provides a five-row operational dashboard covering all service tiers. Six alarms with `TreatMissingData=NOT_BREACHING` prevent false positives during quiet periods. Lambda source maps enable readable stack traces in CloudWatch. X-Ray active tracing on all functions provides end-to-end request visibility.

#### Security
Authentication is enforced via Amazon Cognito JWT on all non-public routes. API Gateway's Cognito authorizer caches results for five minutes, reducing latency without compromising security. IAM policies follow least privilege — each Lambda function has a dedicated role with only the specific DynamoDB actions it requires (e.g., OrderProcessorFunction has only `GetItem` and `UpdateItem`, not `PutItem` or `Scan`). S3 is private with HTTPS enforced. CloudFront redirects all HTTP to HTTPS. WAF blocks known malicious IPs, OWASP Top 10 attack patterns, and rate-abusive clients. CORS uses an explicit allowed origin (the CloudFront domain) rather than a wildcard. No hardcoded credentials exist anywhere in the codebase — all sensitive values are read from environment variables at CDK synth time or from the runtime `config.json` injected at deploy time.

#### Reliability
SQS `maxReceiveCount=3` ensures failed order processing attempts are retried before routing to the DLQ. `visibilityTimeout=180s` is six times the Lambda timeout (30s), following AWS best practice. DLQ retention of 14 days provides ample time for investigation and replay. `ConditionExpression: attribute_not_exists(PK)` on order creation prevents duplicate orders under retry conditions. The Order Processor checks `status !== 'PENDING'` before processing, providing idempotency against duplicate SQS delivery. DynamoDB PITR allows point-in-time recovery to any second in the last 35 days.

#### Performance Efficiency
All Lambda functions run on ARM64 Graviton2, providing approximately 20% better price-performance compared to x86. Node.js 22 with esbuild bundling and minification reduces cold-start package size. AWS SDK v3 is bundled directly, eliminating runtime version dependencies. DynamoDB PAY_PER_REQUEST scales to zero at zero traffic and handles any burst automatically. CloudFront's CachingOptimized behavior with Vite content-hashed filenames allows browser caches to hold assets indefinitely, reducing origin load. The CACHING_DISABLED behavior on `index.html` and `config.json` ensures deployments are reflected immediately. HTTP/2 and HTTP/3 reduce connection overhead for the SPA.

#### Cost Optimization
The entire platform is serverless — there is zero cost when idle. DynamoDB PAY_PER_REQUEST means no over-provisioned capacity units. Lambda is billed per 100ms invocation. CloudFront PRICE_CLASS_200 limits edge locations to US, Europe, and Israel, reducing CDN cost without significant latency impact for the target region (ap-southeast-1 is the origin). CloudWatch log groups have explicit 30-day retention policies, preventing unbounded storage costs. No EC2, no RDS, no ALB, and no NAT Gateway costs.

#### Sustainability
ARM64 Graviton2 processors consume less energy per unit of compute compared to x86. CloudFront caching reduces the number of Lambda invocations and DynamoDB reads required to serve repeat requests. PAY_PER_REQUEST means compute resources are allocated only when actually needed, with zero idle consumption.


## 8. Timeline

| Phase | Description | Duration |
|-------|-------------|---------|
| **Phase 1** | Infrastructure foundation: CDK project setup, Cognito, DynamoDB single-table design, API Gateway, and initial Lambda scaffolding | Week 1 |
| **Phase 2–4** | Core API development: ProductServiceFunction, CartServiceFunction, OrderServiceFunction, and PaymentServiceFunction; EventBridge and SQS setup | Week 1–2 |
| **Phase 5–6** | Event-driven order processing: custom EventBridge bus, OrderQueue + DLQ, OrderProcessorFunction, and X-Ray tracing | Week 2 |
| **Phase 7** | Frontend delivery: React 19 + Vite SPA, FrontendStack (S3 + CloudFront OAC), runtime config injection via `config.json`, and Cognito SDK integration | Week 2–3 |
| **Phase 8** | Security hardening: WAF WebACL (SecurityStack in us-east-1), IP reputation, OWASP CRS, Known Bad Inputs, and rate limiting | Week 3 |
| **Phase 9** | Observability: MonitoringStack (CloudWatch dashboard, alarms, SNS email subscription), logging, and alert wiring | Week 3 |
| **Phase 10** | UX refinement: search, price sorting, order status polling, and frontend cleanup | Week 4 |
| **Finalization** | Backend/frontend hardening, security verification, testing, and documentation | Week 4 |


## 9. Cost Estimation

For reference, the supporting estimate file can be downloaded here: <a href="/files/my-estimate.csv" download>Download my-estimate.csv</a>.

All cost estimates use the **AWS Asia Pacific (Singapore) ap-southeast-1** region and reflect expected usage for a demo-scale platform with moderate traffic (approximately 1,000 API requests per day). Figures are calculated using the **AWS Pricing Calculator** (ap-southeast-1, July 2025).

| Service | Usage Assumption | Estimated Monthly Cost |
|---------|-----------------|----------------------|
| AWS Lambda | 30,000 invocations/month, ARM64, 512 MB, avg 200ms | ~$0.00 (free tier: 1M invocations) |
| Amazon API Gateway | 30,000 REST API calls/month | ~$0.13 |
| Amazon DynamoDB | PAY_PER_REQUEST, 0.1 GB storage, PITR 1 GB | ~$0.27 |
| Amazon SQS | ~1,000 order messages/month | ~$0.00 (free tier: 1M requests) |
| Amazon EventBridge | ~3,000 custom events/month | ~$0.00 (free tier: 1M events) |
| Amazon S3 | ~10 MB SPA assets, 100 PUT requests | ~$0.00 |
| Amazon CloudFront | ~1 GB data transfer/month, PRICE_CLASS_200 | ~$0.14 |
| **AWS WAF** | 1 WebACL, 1 custom rule, 3 managed rule groups, ~30,000 requests | **~$9.02** |
| Amazon Cognito | <50,000 MAU, Lite Tier | ~$0.01 |
| Amazon CloudWatch | 1 dashboard, 6 alarms, 0.5 GB logs/month | ~$0.95 |
| Amazon SNS | <1,000 email notifications/month | ~$0.00 |
| **Total** | | **$10,92/tháng (~$131,07/năm)** |

The dominant cost is **AWS WAF (~$9.02/month, 86% of total)** comprising: $5.00 WebACL flat fee + $3.00 for 3 managed rule groups + $1.00 for 1 custom rate-limit rule. At zero traffic, the platform costs approximately **$9.10/month** (WAF fixed fees + CloudWatch only).

For a production-scale deployment with 100,000 requests/day and 10,000 monthly active users, estimated cost would be approximately **$40–$70/month**, still substantially below equivalent server-based deployments.


## 10. Risk Assessment

#### Risk Matrix

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|-----------|
| Lambda cold starts affecting perceived latency | Medium | Low | ARM64 Graviton2 reduces cold start duration; esbuild bundling minimizes package size; DynamoDB PAY_PER_REQUEST eliminates warm-up waits |
| DynamoDB hot partition under high write concurrency | Low | High | Single-table design uses randomized `orderId` (UUID v4) as partition key prefix, distributing writes across partitions evenly |
| SQS message processing failure causing stuck orders | Low | High | DLQ with 14-day retention and `maxReceiveCount=3` prevents message loss; 6× visibility timeout prevents premature requeue; CloudWatch alarm fires on first DLQ message |
| Cognito JWT expiry causing 401 during long sessions | Medium | Medium | Axios response interceptor performs silent token refresh via Cognito SDK on 401; redirects to login only if refresh token is also expired |
| WAF false positives blocking legitimate requests | Low | Medium | Managed rule groups are set to block (not count-only) but can be switched to COUNT mode via CDK override if false positives are observed |
| Cross-region WAF ARN dependency (SecurityStack → FrontendStack) | Low | Medium | WAF ARN is passed as env var (`WAF_WEB_ACL_ARN`) rather than `Fn.importValue` to work around the cross-region CloudFormation export limitation; documented in `.env.example` |
| ALLOWED_ORIGIN misconfiguration causing CORS failures | Medium | High | `ALLOWED_ORIGIN` must be set to the CloudFront URL after FrontendStack deploy; documented in `.env.example`; API falls back to `localhost:5173` if empty |
| Account-level Lambda concurrency exhaustion | Low | Medium | Serverless platform with no reserved concurrency shares the account pool; adding reserved concurrency to OrderProcessorFunction would isolate the order pipeline |

#### Contingency Plans

- All stacks are independently redeployable via `npx cdk deploy <StackName>`.
- DynamoDB PITR allows recovery to any second within the last 35 days.
- The `dist/` build output directory and all CDK stacks are fully reproducible from source code.


## 11. Expected Outcomes

#### Technical Deliverables

1. **Eight independently deployable CDK stacks** covering the complete platform — authentication, database, API, event processing, monitoring, security, and frontend delivery.

2. **Three production Lambda functions** handling public product catalog browsing (with category filtering, text search, price sorting, and slug-based URLs) and authenticated cart and order management.

3. **Event-driven order processing pipeline** demonstrating asynchronous architecture: API decoupled from processing, SQS as a reliable buffer, DLQ for failure isolation, and idempotent state transitions in DynamoDB.

4. **Secure customer-facing SPA** with Cognito authentication, real-time order status polling (3-second interval with terminal-state detection), and responsive design.

5. **Complete observability stack** with a five-row CloudWatch dashboard, six operational alarms, email notification via SNS, and X-Ray traces across all Lambda functions.

6. **WAF protection** covering IP reputation, OWASP Top 10, known exploit patterns, and per-IP rate limiting.

#### Learning Outcomes

- Applied AWS CDK TypeScript for infrastructure-as-code across 8 stacks and ~2,000 lines of CDK definitions.
- Implemented DynamoDB single-table design with GSI-based access patterns and zero Scan operations.
- Built an event-driven asynchronous processing pipeline with EventBridge, SQS, and Lambda.
- Applied the AWS Well-Architected Framework across all six pillars in a real deployment.
- Delivered a full-stack serverless application from a blank AWS account to a publicly accessible CloudFront URL.

#### Well-Architected Alignment

The final deployed system achieves the following scores against the AWS Well-Architected Framework:

| Pillar | Status |
|--------|--------|
| Operational Excellence | ✅ IaC, structured logging, CloudWatch Dashboard, 6 alarms, X-Ray |
| Security | ✅ Cognito JWT, least-privilege IAM, WAF, private S3, TLS 1.2+, no wildcards |
| Reliability | ✅ DLQ, retry logic, PITR, idempotency, SQS visibility timeout best practice |
| Performance Efficiency | ✅ ARM64, Node.js 22, esbuild, PAY_PER_REQUEST, CloudFront caching |
| Cost Optimization | ✅ Zero idle cost, no over-provisioned resources, log retention policies |
| Sustainability | ✅ ARM64 energy efficiency, caching reduces compute, scale-to-zero |
