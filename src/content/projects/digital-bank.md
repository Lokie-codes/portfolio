---
title: "Digital Bank"
description: "A scalable, microservices-based digital banking platform providing user management, accounts, loans, cards, transactions, and an agent name generator."
technologies:
  - Spring Boot
  - FastAPI
  - Docker
  - Docker Compose
  - PostgreSQL
  - Apache Kafka
  - Prometheus
  - Grafana
  - Spring Security
githubUrl: "https://github.com/Lokie-codes/digital-bank-v1"
liveUrl: ""
featured: false
---

# Digital Bank V1

A modern, microservices-based digital banking solution built for scalability, security, and observability.

## Features

- **User Management**  
  Manage user profiles, authentication, and authorization endpoints.

- **Accounts**  
  Create checking & savings accounts; support balance inquiries and intra-account transfers.

- **Loans**  
  Submit and track loan applications; automatic EMI calculation and status updates.

- **Cards**  
  Issue, activate, and monitor debit & credit cards; view usage history.

- **Transactions**  
  Perform, record, and query financial transactions; full transaction history with timestamps and statuses.

- **Agent Name Generator**  
  A FastAPI microservice that generates unique “agent” usernames by combining adjectives and nouns from a JSON source.

## Technical Highlights

- **Microservices Architecture**  
  Each domain (users, accounts, loans, cards, transactions, agent-name) runs as an independent Spring Boot or FastAPI service, communicating via REST and Kafka messaging.

- **Containerized with Docker & Docker Compose**  
  One-command setup (`docker-compose up --build`) for all services, database, and monitoring stack.

- **PostgreSQL Database**  
  Durable, production-grade data storage with separate schemas per service.

- **Event-Driven Messaging (Kafka)**  
  Reliable, asynchronous communication between services (e.g., account events, transaction events).

- **Monitoring & Metrics**  
  Prometheus scrapes application metrics; Grafana dashboards visualize service health, request latencies, and resource usage.

- **Security & Configuration**  
  Spring Security handles authentication/authorization; centralized configuration via environment variables and `config/` directory; supports multiple profiles (`SPRING_PROFILES_ACTIVE`).

- **Agent-Name Service**  
  Lightweight Python/FastAPI service with Dockerfile; exposes a REST endpoint for on-the-fly name generation.
