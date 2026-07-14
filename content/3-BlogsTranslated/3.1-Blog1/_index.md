---
title: "Blog 1"
date: 2026-06-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---
# Dual-token Authentication for Nakama Game Servers with Amazon Cognito on AWS

Modern multiplayer games often require two separate authentication mechanisms: one to verify the player's identity and another to manage the in-game session. Although these systems serve different purposes, they must work together seamlessly to avoid interrupting the player experience.

AWS introduces a **Dual-token Authentication** architecture that combines **Amazon Cognito** and **Nakama Game Server**. In this approach, Amazon Cognito authenticates users, while Nakama manages game sessions. A Go runtime hook bridges these two systems by validating the Cognito token and creating a Nakama session token, allowing players to move from login to gameplay without additional authentication steps.

---

## Solution Architecture

The solution is organized into multiple layers to separate authentication, traffic routing, and game session management.

> *Figure 1. Dual-token Authentication architecture for Nakama on AWS.*

The authentication workflow consists of the following steps:

- The player signs in through Amazon Cognito.
- Cognito returns a JWT access token.
- Client requests are routed through Amazon CloudFront.
- AWS WAF inspects incoming traffic before forwarding requests.
- HTTP API requests are routed to an Application Load Balancer (ALB).
- WebSocket traffic is routed through a Network Load Balancer (NLB).
- Nakama running on Amazon ECS validates the JWT using a Go runtime hook.
- Nakama issues its own session token and manages the player's game session.

---

## Key Components

### Amazon Cognito

Amazon Cognito is responsible for player authentication and identity management.

Players authenticate using the **USER_SRP_AUTH** flow, allowing passwords to remain on the client device. After successful authentication, Cognito issues a JWT access token that represents the player's identity.

---

### Go Runtime Hook

A custom Go runtime hook connects Amazon Cognito with Nakama.

Its responsibilities include:

- Validating Cognito JWT tokens.
- Extracting the player's identity from the token.
- Creating a Nakama session.
- Returning a Nakama session token to the client.

This separation allows Cognito to manage authentication while Nakama focuses on gameplay and real-time communication.

---

### Amazon CloudFront

Amazon CloudFront acts as the single HTTPS entry point for all client traffic.

CloudFront automatically routes requests based on their type:

- HTTP API requests are forwarded to the ALB.
- WebSocket connections are forwarded to the NLB.

Using a single endpoint simplifies client configuration while improving performance and security.

---

### Application Load Balancer (ALB)

The ALB handles HTTP-based communication between clients and Nakama.

Its primary responsibilities include:

- Routing API requests.
- Applying path-based routing rules.
- Restricting access to approved endpoints.

This provides an additional layer of security for the application's REST APIs.

---

### Network Load Balancer (NLB)

The NLB manages persistent WebSocket connections.

Operating at the TCP layer, it forwards traffic directly to Nakama without modifying the connection, making it well suited for real-time multiplayer communication.

---

### Amazon ECS on AWS Fargate

Nakama is deployed as containers on Amazon ECS using AWS Fargate.

This serverless container platform enables:

- Automatic scaling.
- Simplified deployment.
- Reduced infrastructure management.

---

## Why Use Both ALB and NLB?

Each load balancer serves a different purpose.

| Component | Purpose |
|-----------|---------|
| Application Load Balancer | Handles HTTP APIs, routing, and request filtering |
| Network Load Balancer | Handles low-latency WebSocket traffic using TCP passthrough |

Amazon CloudFront intelligently routes each request to the appropriate load balancer, allowing both connection types to share a single public endpoint.

---

## Benefits of Dual-token Authentication

This architecture provides several advantages for online multiplayer games:

- Separates identity authentication from game session management.
- Improves security by validating two independent tokens.
- Supports stable WebSocket connections for real-time gameplay.
- Scales easily using managed AWS services.
- Provides a seamless login experience without interrupting gameplay.

---

## Conclusion

Dual-token Authentication demonstrates an effective way to integrate **Amazon Cognito** with **Nakama Game Server** while keeping authentication and session management independent.

By combining **Amazon CloudFront**, **AWS WAF**, **Application Load Balancer**, **Network Load Balancer**, and **Amazon ECS on AWS Fargate**, the solution delivers a secure, scalable, and high-performance architecture for multiplayer games.

For game developers building online services on AWS, this architecture offers a practical approach to improving authentication, security, and player experience while reducing operational complexity.