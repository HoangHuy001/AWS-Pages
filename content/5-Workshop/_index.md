---
title: "Workshop"
date: 2026-07-04
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# AWS Serverless E-commerce Platform — Deployment Workshop

This workshop walks you through deploying the complete AWS Serverless E-commerce Platform from scratch on AWS. By the end, you will have a fully functional, publicly accessible e-commerce application running in **ap-southeast-1 (Singapore)**.

**Estimated time:** 30 minutes  
**Deployment region:** ap-southeast-1 (Singapore)  
**WAF region:** us-east-1 (required for CloudFront)

---

## Architecture Overview

> **Note:** This is a simplified deployment overview. Refer to Proposal Section 5 for the complete production architecture including WAF rule details, X-Ray tracing, log retention, and security headers configuration.

This is a simplified deployment overview. Refer to Proposal Section 5 for the complete production architecture.

> **Important for full end-to-end testing:** the checkout flow includes a payment step via VNPay. Before deploying the backend, prepare VNPay credentials in AWS Secrets Manager; otherwise the payment route will not work even though the rest of the storefront can still be deployed.

![AWS Serverless Architecture](/images/2-Proposal/diagram.png)


---

## Workshop Contents

1. [Environment Setup](5.1-environment-setup/)
2. [Deploy Backend Stacks](5.2-deploy-backend/)
3. [Deploy Frontend Stack](5.3-deploy-frontend/)
4. [Seed Data & Create Users](5.4-seed-and-users/)
5. [Cleanup](5.5-cleanup/)
