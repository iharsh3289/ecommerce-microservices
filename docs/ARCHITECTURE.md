# 🏗️ Microservices Architecture Guide

## Table of Contents
1. [Architecture Overview](#architecture-overview)
2. [Service Communication](#service-communication)
3. [Database Strategy](#database-strategy)
4. [API Gateway Pattern](#api-gateway-pattern)
5. [Service Discovery](#service-discovery)
6. [Resilience Patterns](#resilience-patterns)
7. [Security](#security)
8. [Deployment](#deployment)

## Architecture Overview

### Microservices Pattern

This ecommerce platform follows a **microservices architecture** where:

- Each service is independently deployable
- Services own their data
- Communication is through well-defined APIs
- Services are organized around business capabilities

### Key Principles

1. **Single Responsibility**: Each service handles one business domain
2. **Loose Coupling**: Services are independent and minimal dependencies
3. **High Cohesion**: Related functionality grouped together
4. **Scalability**: Each service can be scaled independently
5. **Fault Isolation**: Failure in one service doesn't cascade

## Service Communication

### Synchronous Communication (REST)

Services communicate using REST APIs through the API Gateway:

```
Client → API Gateway → Service A → Service B
```

### Asynchronous Communication (Kafka)

Event-driven communication for operations that don't need immediate response:

```
Service A (Producer) → Kafka Topic → Service B (Consumer)
```

### Example: Order Processing Flow

1. **Synchronous**: Order Service calls Inventory Service to check stock
2. **Asynchronous**: Order Service publishes "OrderCreated" event
3. **Event Subscribers**: Payment Service, Notification Service, Shipping Service consume the event

## Database Strategy

### Database Per Service

Each service has its own database to ensure:
- Loose coupling
- Independent scaling
- Technology diversity (MySQL, PostgreSQL, MongoDB)
- Easy horizontal scaling

### Data Consistency

#### Immediate Consistency (Within a Service)
- Single database transactions

#### Eventually Consistent (Between Services)
- Using Saga pattern with events
- Compensation transactions for rollbacks

### Example: Order Creation

```
Order Service:
  1. Create order in database
  2. Publish "OrderCreated" event

Inventory Service:
  1. Listen for "OrderCreated" event
  2. Reserve stock
  3. Publish "StockReserved" event

Payment Service:
  1. Listen for "OrderCreated" event
  2. Process payment
  3. Publish "PaymentProcessed" event

Shipping Service:
  1. Listen for "PaymentProcessed" event
  2. Create shipment
  3. Publish "ShipmentCreated" event
```

## API Gateway Pattern

### Purpose

The API Gateway serves as the single entry point for all client requests:

- **Routing**: Routes requests to appropriate services
- **Load Balancing**: Distributes load across service instances
- **Rate Limiting**: Controls request rate
- **Authentication**: Validates JWT tokens
- **Response Aggregation**: Combines responses from multiple services

### API Gateway Configuration

```
Client Request
    ↓
API Gateway (Port 8888)
    ├→ /auth/** → Auth Service (8081)
    ├→ /products/** → Product Service (8080)
    ├→ /orders/** → Order Service (8082)
    ├→ /payments/** → Payment Service (8084)
    └→ /... → Other Services
```

### Example Request Flow

```
POST /api/v1/orders
↓
API Gateway
├ 1. Extract JWT token
├ 2. Validate token with Auth Service
├ 3. Route to Order Service
├ 4. Order Service checks inventory with Inventory Service
├ 5. Order Service publishes event
├ 6. Response sent back through gateway
↓
Client receives 201 Created
```

## Service Discovery

### Eureka Server

Services register themselves with Eureka for dynamic discovery:

1. **Registration**: Service starts → Registers with Eureka
2. **Heartbeat**: Service periodically sends heartbeat
3. **Discovery**: API Gateway queries Eureka for service instances
4. **Load Balancing**: Ribbon client-side load balancing

### Registration Example

```java
// Service registers automatically when Spring Boot starts
@SpringBootApplication
@EnableEurekaClient
public class ProductServiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(ProductServiceApplication.class, args);
    }
}
```

### Configuration

```yaml
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/
  instance:
    prefer-ip-address: true
```

## Resilience Patterns

### Circuit Breaker (Hystrix)

Prevents cascading failures when a service is down:

```
State: CLOSED → (after threshold failures) → OPEN → (after timeout) → HALF_OPEN → CLOSED
```

### Retry Logic

Automatically retries failed requests:

```java
@Retry(maxAttempts = 3, delay = 1000)
public OrderDto getOrder(Long orderId) {
    return orderService.findById(orderId);
}
```

### Timeout

Prevents hanging requests:

```yaml
feign:
  client:
    config:
      default:
        readTimeout: 5000
        connectTimeout: 3000
```

### Fallback Responses

Graceful degradation when service fails:

```java
@HystrixCommand(fallbackMethod = "getFallbackProducts")
public List<ProductDto> getProducts() {
    return productService.getAllProducts();
}

public List<ProductDto> getFallbackProducts() {
    return Collections.emptyList();
}
```

## Security

### Authentication Flow

1. User sends credentials to Auth Service
2. Auth Service validates and returns JWT token
3. Client includes JWT in all subsequent requests
4. API Gateway validates JWT before routing

### Token Structure

```
Header.Payload.Signature

Header: {
  "alg": "HS512",
  "typ": "JWT"
}

Payload: {
  "sub": "user@example.com",
  "role": "ADMIN",
  "iat": 1626200000,
  "exp": 1626286400
}
```

### Authorization

Services check user roles in JWT token:

```java
@PreAuthorize("hasRole('ADMIN')")
@PostMapping("/products")
public ProductDto createProduct(@RequestBody ProductDto dto) {
    return productService.save(dto);
}
```

## Deployment

### Docker Containerization

Each service is containerized:

```dockerfile
FROM maven:latest as builder
WORKDIR /app
COPY . .
RUN mvn clean package

FROM openjdk:17
COPY --from=builder /app/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

### Docker Compose Orchestration

```yaml
version: '3.8'
services:
  discovery-service:
    image: ecommerce/discovery-service
    ports:
      - "8761:8761"
  
  product-service:
    image: ecommerce/product-service
    depends_on:
      - discovery-service
      - mysql
    environment:
      - EUREKA_SERVER_URL=http://discovery-service:8761
      - DB_URL=jdbc:mysql://mysql:3306/products
```

### Deployment Steps

1. Build Docker images
2. Push to Docker registry
3. Deploy using Docker Compose or Kubernetes
4. Configure environment variables
5. Set up monitoring and logging

---

**For more details, see the README.md and service-specific documentation.**