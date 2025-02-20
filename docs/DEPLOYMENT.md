# 🐳 Docker & Deployment Guide

## Table of Contents
1. [Docker Overview](#docker-overview)
2. [Building Docker Images](#building-docker-images)
3. [Docker Compose Setup](#docker-compose-setup)
4. [Running Services with Docker](#running-services-with-docker)
5. [Docker Networking](#docker-networking)
6. [Volumes and Persistence](#volumes-and-persistence)
7. [Troubleshooting](#troubleshooting)

## Docker Overview

All 16 microservices are containerized for:
- **Consistency**: Same environment across dev, test, and production
- **Scalability**: Easy to scale services independently
- **Portability**: Run on any machine with Docker
- **Isolation**: Services don't interfere with each other

## Building Docker Images

### Individual Service Build

Each service has a Dockerfile for containerization:

```bash
# Navigate to service directory
cd product-service

# Build image
docker build -t ecommerce/product-service:latest .

# Verify image
docker images | grep product-service
```

### Build All Services

```bash
# From root directory, build all images
docker-compose build

# Build specific service
docker-compose build product-service

# Build without cache
docker-compose build --no-cache
```

### Dockerfile Structure

```dockerfile
# Multi-stage build for optimization
FROM maven:latest as builder
VOLUME /tmp
ARG PROJECT_VERSION=0.0.1
WORKDIR /home/app
COPY ./ .
RUN mvn clean package -DskipTests

# Runtime stage
FROM openjdk:17-slim
RUN mkdir -p /home/app
WORKDIR /home/app
COPY --from=builder /home/app/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
CMD ["mvn", "spring-boot:run"]
```

## Docker Compose Setup

### Configuration File

`docker-compose.yml` orchestrates all services:

```yaml
version: '3.8'

services:
  # Discovery Service (Eureka)
  discovery-service:
    build: ./discovery-service
    container_name: discovery-service
    ports:
      - "8761:8761"
    environment:
      - SPRING_APPLICATION_NAME=discovery-service
    networks:
      - ecommerce-network

  # API Gateway
  api-gateway:
    build: ./api-gateway
    container_name: api-gateway
    ports:
      - "8888:8888"
    depends_on:
      - discovery-service
    environment:
      - EUREKA_CLIENT_SERVICEURL_DEFAULTZONE=http://discovery-service:8761/eureka/
    networks:
      - ecommerce-network

  # Database Services
  mysql:
    image: mysql:8.0
    container_name: mysql-db
    ports:
      - "3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: root123
      MYSQL_DATABASE: ecommerce
    volumes:
      - mysql_data:/var/lib/mysql
    networks:
      - ecommerce-network

  # Message Broker
  kafka:
    image: confluentinc/cp-kafka:7.0.0
    container_name: kafka
    ports:
      - "9092:9092"
    depends_on:
      - zookeeper
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:29092,PLAINTEXT_HOST://kafka:9092
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: PLAINTEXT:PLAINTEXT,PLAINTEXT_HOST:PLAINTEXT
      KAFKA_INTER_BROKER_LISTENER_NAME: PLAINTEXT
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
    networks:
      - ecommerce-network

  zookeeper:
    image: confluentinc/cp-zookeeper:7.0.0
    container_name: zookeeper
    ports:
      - "2181:2181"
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000
    networks:
      - ecommerce-network

  # All Microservices...
  auth-service:
    build: ./auth-service
    container_name: auth-service
    ports:
      - "8081:8081"
    depends_on:
      - discovery-service
      - mysql
    environment:
      - EUREKA_CLIENT_SERVICEURL_DEFAULTZONE=http://discovery-service:8761/eureka/
      - SPRING_DATASOURCE_URL=jdbc:mysql://mysql:3306/ecommerce
      - SPRING_DATASOURCE_USERNAME=root
      - SPRING_DATASOURCE_PASSWORD=root123
    networks:
      - ecommerce-network

  # ... more services ...

networks:
  ecommerce-network:
    driver: bridge

volumes:
  mysql_data:
  postgres_data:
  elasticsearch_data:
  mongodb_data:
```

## Running Services with Docker

### Start All Services

```bash
# Start in detached mode (background)
docker-compose up -d

# Start and view logs
docker-compose up

# Start specific services
docker-compose up -d discovery-service api-gateway mysql
```

### Check Service Status

```bash
# View all running containers
docker-compose ps

# View specific service status
docker-compose ps api-gateway

# View logs
docker-compose logs -f                    # All services
docker-compose logs -f product-service    # Specific service
docker-compose logs --tail=50 api-gateway # Last 50 lines
```

### Stop Services

```bash
# Stop all services
docker-compose down

# Stop specific service
docker-compose stop product-service

# Stop and remove volumes (data loss!)
docker-compose down -v
```

### Restart Services

```bash
# Restart all
docker-compose restart

# Restart specific
docker-compose restart payment-service

# Restart with fresh build
docker-compose up -d --build product-service
```

## Docker Networking

### Network Architecture

All services connect through a bridge network `ecommerce-network`:

```
┌─────────────────────────────────────┐
│      ecommerce-network              │
│  (Docker Bridge Network)            │
│                                     │
│  ┌────────────┐  ┌──────────────┐  │
│  │ Product    │  │ Order        │  │
│  │ Service    ├──┤ Service      │  │
│  └────────────┘  └──────────────┘  │
│         │               │            │
│         └───────┬───────┘            │
│                 │                    │
│        ┌────────▼────────┐           │
│        │ MySQL Database  │           │
│        └─────────────────┘           │
│                                      │
└──────────────────────────────────────┘
```

### Service-to-Service Communication

Services can communicate using service names:

```java
// Instead of localhost:8080
// Use service name (from docker-compose)
RestTemplate restTemplate = new RestTemplate();
String url = "http://product-service:8080/products";
ProductDto product = restTemplate.getForObject(url, ProductDto.class);
```

### Port Mapping

Internal container ports don't need to match external ports:

```yaml
services:
  product-service:
    ports:
      - "8080:8080"    # External:Internal
      # Can also be: "9000:8080" to access via 9000
```

## Volumes and Persistence

### Database Volumes

Persist data across container restarts:

```yaml
volumes:
  mysql_data:
    driver: local
  postgres_data:
    driver: local
  elasticsearch_data:
    driver: local

services:
  mysql:
    volumes:
      - mysql_data:/var/lib/mysql
  
  postgres:
    volumes:
      - postgres_data:/var/lib/postgresql/data
```

### Named Volumes

```bash
# List volumes
docker volume ls

# Inspect volume
docker volume inspect ecommerce_mysql_data

# Remove unused volumes
docker volume prune

# Remove specific volume (data loss!)
docker volume rm ecommerce_mysql_data
```

### Bind Mounts (Development)

Mount local directory for development:

```yaml
services:
  product-service:
    volumes:
      - ./product-service/src:/home/app/src  # Live code reload
      - ./product-service/target:/home/app/target
```

## Troubleshooting

### Check Service Logs

```bash
# View all logs
docker-compose logs

# View recent logs
docker-compose logs --tail=100

# Follow logs in real-time
docker-compose logs -f auth-service

# View logs since specific time
docker-compose logs --since 2024-01-15 product-service
```

### Debug Container

```bash
# Execute command in running container
docker-compose exec product-service bash

# View container details
docker inspect product-service

# Check network connectivity
docker-compose exec product-service ping order-service
```

### Service Not Starting

```bash
# Check service status
docker-compose ps

# View service logs for errors
docker-compose logs product-service

# Check if port is already in use
lsof -i :8080  # macOS/Linux
netstat -ano | findstr :8080  # Windows

# Force recreate container
docker-compose up -d --force-recreate product-service
```

### Database Connection Issues

```bash
# Test database connection from service
docker-compose exec product-service \
  mysql -h mysql -u root -proot123 ecommerce

# Check if database service is running
docker-compose ps mysql

# View database logs
docker-compose logs mysql
```

### Network Issues

```bash
# Verify network exists
docker network ls

# Inspect network
docker network inspect ecommerce_ecommerce-network

# Test service connectivity
docker-compose exec api-gateway ping product-service

# Check DNS resolution
docker-compose exec api-gateway nslookup product-service
```

### Rebuild and Restart

```bash
# Full reset (removes containers and volumes!)
docker-compose down -v
docker-compose build --no-cache
docker-compose up -d

# Rebuild specific service
docker-compose build --no-cache product-service
docker-compose up -d product-service
```

### Resource Issues

```bash
# Check Docker stats
docker stats

# Limit container resources
docker-compose up -d --compatible

# Check available disk space
df -h  # macOS/Linux
dir  # Windows
```

## Production Deployment

### Docker Registry

Push images to registry:

```bash
# Tag image
docker tag ecommerce/product-service:latest \
  myregistry.azurecr.io/ecommerce/product-service:latest

# Login to registry
docker login myregistry.azurecr.io

# Push image
docker push myregistry.azurecr.io/ecommerce/product-service:latest
```

### Kubernetes Deployment

Deploy using Kubernetes:

```bash
# Create deployment
kubectl create deployment product-service \
  --image=myregistry.azurecr.io/ecommerce/product-service:latest

# Scale deployment
kubectl scale deployment product-service --replicas=3

# Check pods
kubectl get pods
```

---

**For more information, see the main README.md**