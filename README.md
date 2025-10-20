# Node.js Microservices Guide

Complete guide to building microservices architecture with Node.js, Docker, and Kubernetes

## Table of Contents

1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Architecture Overview](#architecture-overview)
4. [Getting Started](#getting-started)
5. [Building Microservices](#building-microservices)
6. [API Gateway](#api-gateway)
7. [Service Discovery](#service-discovery)
8. [Inter-Service Communication](#inter-service-communication)
9. [Data Management](#data-management)
10. [Docker Containerization](#docker-containerization)
11. [Kubernetes Deployment](#kubernetes-deployment)
12. [Monitoring & Logging](#monitoring--logging)
13. [Best Practices](#best-practices)

## Introduction

Microservices architecture breaks down applications into small, independent services that communicate over well-defined APIs. This guide will walk you through building a production-ready microservices system.

## Prerequisites

- Node.js 18+
- Docker Desktop
- Kubernetes (minikube or Docker Desktop)
- Basic understanding of REST APIs
- Familiarity with Docker and containers

## Architecture Overview

```
┌─────────────┐
│  API Gateway │
└──────┬──────┘
       │
   ┌───┴───────┬──────────┬──────────┐
   │           │          │          │
┌──▼──┐   ┌───▼───┐  ┌───▼───┐  ┌──▼────┐
│User │   │Order  │  │Product│  │Payment│
│Svc  │   │Svc    │  │Svc    │  │Svc    │
└──┬──┘   └───┬───┘  └───┬───┘  └──┬────┘
   │          │          │         │
   └──────────┴──────────┴─────────┘
              Message Queue
```

## Getting Started

### Installation

```bash
npm install express
npm install @hapi/boom
npm install dotenv
npm install helmet
npm install morgan
```

### Basic Service Structure

```javascript
const express = require('express');
const helmet = require('helmet');
const morgan = require('morgan');

const app = express();
const PORT = process.env.PORT || 3000;

app.use(helmet());
app.use(morgan('combined'));
app.use(express.json());

app.get('/health', (req, res) => {
  res.status(200).json({ status: 'UP' });
});

app.listen(PORT, () => {
  console.log(`Service running on port ${PORT}`);
});
```

## Building Microservices

### User Service

```javascript
// user-service/index.js
const express = require('express');
const router = express.Router();

router.post('/users', async (req, res) => {
  // User creation logic
});

router.get('/users/:id', async (req, res) => {
  // User retrieval logic
});

module.exports = router;
```

## Docker Containerization

### Dockerfile

```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .

EXPOSE 3000

CMD ["node", "index.js"]
```

### docker-compose.yml

```yaml
version: '3.8'

services:
  user-service:
    build: ./user-service
    ports:
      - "3001:3000"
    environment:
      - NODE_ENV=production
      - DB_HOST=postgres
  
  order-service:
    build: ./order-service
    ports:
      - "3002:3000"
    environment:
      - NODE_ENV=production
```

## Kubernetes Deployment

### Deployment Configuration

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: user-service
spec:
  replicas: 3
  selector:
    matchLabels:
      app: user-service
  template:
    metadata:
      labels:
        app: user-service
    spec:
      containers:
      - name: user-service
        image: user-service:latest
        ports:
        - containerPort: 3000
```

## Monitoring & Logging

### Health Checks

```javascript
app.get('/health', (req, res) => {
  res.status(200).json({
    status: 'UP',
    timestamp: new Date().toISOString(),
    uptime: process.uptime()
  });
});
```

## Best Practices

1. **Single Responsibility** - Each service should do one thing well
2. **API Versioning** - Always version your APIs
3. **Error Handling** - Implement consistent error handling
4. **Logging** - Use structured logging (Winston, Pino)
5. **Security** - Always use HTTPS, implement rate limiting
6. **Testing** - Write unit and integration tests
7. **Documentation** - Use Swagger/OpenAPI

## Resources

- [Microservices Patterns](https://microservices.io/patterns/)
- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)
- [12 Factor App](https://12factor.net/)

## Contributing

Contributions welcome! Please read [CONTRIBUTING.md](CONTRIBUTING.md).

## License

MIT License - see [LICENSE](LICENSE) for details
