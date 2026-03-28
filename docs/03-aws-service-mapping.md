# ☁️ AWS Service Mapping – ShopSphere Platform

## 📌 Overview

This document maps every major system component of the ShopSphere platform to specific AWS services.

It explains:
- Why each AWS service is used
- How it fits into the architecture
- Trade-offs and alternatives
- Cost and scalability considerations

This phase translates the high-level architecture into real AWS implementation decisions.

---

## 🧠 Design Principle

All service choices follow:

- AWS Well-Architected Framework
- Microservices-first design
- Serverless where possible
- Managed services over self-managed infrastructure
- Global scalability by default

---

# 🌍 1. Edge Layer (Global Traffic Management)

## 🎯 Purpose
Handle global user traffic, improve latency, and protect against attacks.

---

## 🔧 AWS Services Used

### 🌐 Amazon Route 53
- Global DNS routing
- Latency-based routing
- Health checks for failover

**Why used:**
- Routes users to nearest healthy region
- Supports multi-region failover

**Alternative:**
- Cloudflare DNS (external option)

---

### 🚀 Amazon CloudFront
- Global Content Delivery Network (CDN)
- Caches static and dynamic content

**Why used:**
- Reduces latency (<100ms target)
- Offloads origin servers

**Best for:**
- Images
- JS/CSS
- Product pages caching

---

### 🛡️ AWS WAF (Web Application Firewall)
- Filters malicious traffic
- Blocks SQL injection & XSS attacks

**Why used:**
- Protects APIs and web app

---

### 🔥 AWS Shield (Standard)
- DDoS protection at network layer

**Why used:**
- Protects against large-scale attacks during peak sales

---

# ⚙️ 2. Application Layer (Microservices)

## 🎯 Purpose
Handle all business logic for the platform.

---

## 🔧 AWS Services Used

### 🧩 Amazon ECS with Fargate
- Runs containerized microservices
- No server management required

**Services deployed here:**
- User Service
- Product Service
- Order Service
- Cart Service

**Why ECS Fargate:**
- Serverless containers
- Auto scaling built-in
- Lower operational overhead

---

### ⚡ AWS Lambda
- Event-driven serverless functions

**Used for:**
- Image processing
- Order confirmations
- Notifications
- Lightweight APIs

**Why Lambda:**
- Pay-per-use
- Automatically scales to zero

---

### 🔀 Application Load Balancer (ALB)
- Distributes traffic across microservices

**Why ALB:**
- Path-based routing (`/users`, `/orders`)
- Supports containers and Lambda targets

---

# 🗄️ 3. Data Layer

## 🎯 Purpose
Store structured, unstructured, and fast-access data.

---

## 🔧 AWS Services Used

### 🟡 Amazon Aurora (MySQL/PostgreSQL)
- Relational database

**Used for:**
- Orders
- Payments
- Transactions

**Why Aurora:**
- High performance
- Multi-AZ replication
- Automatic failover

---

### 🔵 Amazon DynamoDB
- NoSQL key-value database

**Used for:**
- User sessions
- Shopping cart
- Product metadata (fast access)

**Why DynamoDB:**
- Millisecond latency
- Auto scaling
- Serverless database

---

### ⚡ Amazon ElastiCache (Redis)
- In-memory caching layer

**Used for:**
- Product listings cache
- Session caching
- Frequently accessed data

**Why Redis:**
- Reduces database load
- Improves response time (<100ms requirement)

---

### 🪣 Amazon S3
- Object storage (data lake foundation)

**Used for:**
- User activity logs
- Event streaming storage
- ML training datasets
- Product images

**Why S3:**
- Infinite scalability
- Very low cost
- Durable storage (11 9s)

---

# 🔄 4. Event Streaming Layer

## 🎯 Purpose
Capture real-time user behavior for analytics and ML.

---

## 🔧 AWS Services Used

### 📡 Amazon Kinesis Data Streams
- Real-time event ingestion system

**Events captured:**
- Product views
- Add to cart
- Purchases
- Click behavior

**Why Kinesis:**
- Real-time processing (< seconds)
- High throughput (supports 100K+ RPS)

---

### 🔁 Amazon Kinesis Data Firehose
- Loads streaming data into S3/Redshift

**Why used:**
- Automatic data delivery
- No manual pipeline management

---

# 📊 5. Analytics Layer

## 🎯 Purpose
Turn raw data into business insights.

---

## 🔧 AWS Services Used

### 📊 Amazon Athena
- SQL queries on S3 data

**Why used:**
- Serverless analytics
- No infrastructure required

---

### 🏢 Amazon Redshift
- Data warehouse

**Used for:**
- Large-scale analytics
- Business reporting

**Why Redshift:**
- High performance for complex queries
- Columnar storage optimized for analytics

---

### 📈 Amazon QuickSight
- Business intelligence dashboards

**Used by:**
- Marketing teams
- Product teams
- Executives

---

# 🧠 6. Machine Learning Layer

## 🎯 Purpose
Power recommendations and predictive analytics.

---

## 🔧 AWS Services Used

### 🤖 Amazon SageMaker
- Full ML platform

**Used for:**
- Recommendation engine
- Fraud detection
- Demand forecasting

**Why SageMaker:**
- Managed ML training
- Built-in deployment
- Supports real-time inference

---

### 🧠 AWS Personalize (Optional Enhancement)
- Real-time recommendation engine

**Why considered:**
- Pre-built recommendation system
- Faster implementation than custom ML

---

# 🔐 7. Security Layer

## 🎯 Purpose
Protect data, users, and infrastructure.

---

## 🔧 AWS Services Used

### 🔑 AWS IAM
- Access control and permissions

### 🔐 AWS KMS
- Encryption key management

### 🔒 AWS Secrets Manager
- Secure storage of credentials

### 🛡️ AWS WAF + Shield
- Application + network protection

---

# 📦 8. DevOps & CI/CD

## 🎯 Purpose
Automate deployment and updates.

---

## 🔧 AWS Services Used

### 🔧 AWS CodePipeline
- CI/CD orchestration

### 🏗️ AWS CodeBuild
- Build and test applications

### 🚀 AWS CodeDeploy
- Automated deployment to ECS/Lambda

### 📦 Amazon ECR
- Docker container registry

---

# 📡 9. Monitoring & Observability

## 🎯 Purpose
Track system health and performance.

---

## 🔧 AWS Services Used

### 📊 Amazon CloudWatch
- Metrics, logs, dashboards

### 🔍 AWS X-Ray
- Distributed tracing

### 🧾 AWS CloudTrail
- Audit logs for security compliance

---

# 🌍 10. Multi-Region Architecture

## 🎯 Purpose
Ensure global uptime and disaster recovery.

---

## 🔧 AWS Services Used

### 🌐 Route 53 Health Checks
- Automatic failover routing

### 🗄️ Aurora Global Database
- Cross-region database replication

### 🪣 S3 Cross-Region Replication
- Data durability across regions

### 🌍 DynamoDB Global Tables
- Multi-region active-active NoSQL

---

# 💰 11. Cost Optimization Strategy

## 🎯 Approach

- Use serverless wherever possible
- Auto-scaling for all compute services
- S3 lifecycle policies (hot → cold storage)
- Reserved instances for stable workloads
- On-demand scaling for traffic spikes

---

# 🎯 Final Summary

This service mapping ensures:

- Fully serverless-first architecture
- Global scalability and low latency
- High availability (99.99%+)
- Strong security and compliance
- Real-time analytics + AI integration
- Cost-optimized infrastructure

---

## 🚀 Next Phase

Next document:

> **04-data-flow-design.md**

This will show:
- How data moves through the system step-by-step
- User journey mapped to AWS services
- Event-driven flow architecture
- Real-time analytics pipeline