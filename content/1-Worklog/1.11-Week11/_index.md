---
title: "Week 11 Worklog"
date: 2026-06-29
weight: 2
chapter: false
pre: " <b> 1.11. </b> "
---

### Week 11 Objectives:

* Analyze requirements and design the global system architecture for the final internship project leveraging AWS services.
* Deploy secure virtual network topologies and provision foundational storage and database layers for the core application.

### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Receive project requirements and analyze the final capstone scope <br> - Evaluate and select fitting architectural patterns (Microservices/Serverless) <br> - Draft the comprehensive AWS infrastructure diagram | 29/06/2026  | 29/06/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Utilize AWS CDK/CloudFormation to provision network components: <br>&emsp; + Establish a Multi-AZ Amazon VPC topology <br>&emsp; + Configure Public and Private Subnet isolation <br>&emsp; + Set up NAT Gateways and Security Groups | 30/06/2026 | 30/06/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Provision decoupled storage and database tiers: <br>&emsp; + Launch highly available Multi-AZ Amazon RDS instances <br>&emsp; + Create Amazon DynamoDB tables for unstructured entries <br>&emsp; + Configure S3 Buckets for static media holding | 01/07/2026 | 01/07/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Implement compute and business logic components: <br>&emsp; + Set up Amazon ECS (Fargate) tasks for containerized core workloads <br>&emsp; + Write and configure asynchronous AWS Lambda handlers | 02/07/2026 | 02/07/2026      | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - Hands-on Lab Integration and Verification: <br>&emsp; + Validate multi-tier connectivity from computing tasks (ECS/Lambda) to databases (RDS/DynamoDB) <br>&emsp; + Audit and verify granular IAM Roles for all active project resources | 03/07/2026 | 03/07/2026      | <https://cloudjourney.awsstudygroup.com/> |

### Week 11 Achievements:

Completed the comprehensive system architecture design for the final project, aligning with AWS Well-Architected best practices.

Successfully deployed a secure, isolated network infrastructure with clear segregation between public-facing and private backend subnets.

Initialized and interconnected core stateful layers including relational storage (RDS), NoSQL (DynamoDB), and object storage (S3).

Successfully orchestrated container tasks via ECS Fargate and standalone Lambda functions within a verified, restricted VPC mesh environment.
