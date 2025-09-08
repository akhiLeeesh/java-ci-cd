# Java Microservices CI/CD Starter

This project contains multiple Spring Boot microservices with CI/CD setup.

## Services
- discovery-service (Eureka)
- api-gateway (Spring Cloud Gateway)
- product-service (CRUD API)
- order-service (calls product-service)

## Running Locally
```bash
docker-compose up --build
```

## CI/CD
- Jenkinsfile for Jenkins pipeline
- .github/workflows/ci.yml for GitHub Actions
