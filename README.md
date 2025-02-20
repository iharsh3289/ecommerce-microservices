# 🛍️ Ecommerce Microservices Architecture

![ecommerce-microservices](https://socialify.git.ci/hoangtien2k3/ecommerce-microservices/image?description=1&descriptionEditable=%E2%9A%A1%EF%B8%8F%20Microservice%20Architecture%20with%20Spring%20Boot&font=Inter&forks=1&issues=1&language=1&logo=https%3A%2F%2Fi.ibb.co%2FN366vtQ%2Fhoangtien2k3.png&owner=1&pattern=Floating%20Cogs&pulls=1&stargazers=1&theme=Auto)

A complete, production-ready **Microservices Ecommerce Platform** built with Spring Boot, designed with a scalable and distributed architecture. This system provides a comprehensive RESTful API backend for web and mobile applications.

## 📋 Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Services](#services)
- [Prerequisites](#prerequisites)
- [Project Setup](#project-setup)
- [Running Services](#running-services)
- [Docker & Containerization](#docker--containerization)
- [API Documentation](#api-documentation)
- [Technology Stack](#technology-stack)
- [Project Structure](#project-structure)
- [Contributing](#contributing)
- [License](#license)

## 🎯 Overview

This project implements a **microservices-based ecommerce platform** with 16 independent services, each handling specific business domains. The architecture ensures:

- ✅ **Scalability**: Each service can be scaled independently
- ✅ **Resilience**: Services are loosely coupled and fault-tolerant
- ✅ **Maintainability**: Clear separation of concerns
- ✅ **Performance**: Optimized for high throughput and low latency
- ✅ **Dockerization**: All services are containerized for easy deployment
- ✅ **Service Discovery**: Eureka-based service registration and discovery
- ✅ **API Gateway**: Centralized routing and load balancing
- ✅ **Event-Driven**: Kafka-based asynchronous communication
- ✅ **Database Per Service**: Independent databases for each service

## 🏗️ Architecture

### System Architecture Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                      API Gateway (Port 8888)                 │
│                    (Route & Load Balancer)                   │
└────────────┬────────────────────────────────────────────────┘
             │
    ┌────────┴─────────────────────────────────────────────────┐
    │                                                            │
    ├─ Discovery Service (Eureka)                              │
    │   └─ Service Registration & Discovery                    │
    │                                                            │
    ├─ Auth Service (Port 8081)                                │
    │   └─ JWT Authentication, User Management                 │
    │                                                            │
    ├─ Product Service (Port 8080)                             │
    │   └─ Product Catalog Management                          │
    │                                                            │
    ├─ Order Service (Port 8082)                               │
    │   └─ Order Processing & Cart Management                  │
    │                                                            │
    ├─ Payment Service (Port 8084)                             │
    │   └─ Payment Processing & Transactions                   │
    │                                                            │
    ├─ Inventory Service (Port 8085)                           │
    │   └─ Stock Management                                    │
    │                                                            │
    ├─ Notification Service (Port 8083)                        │
    │   └─ Email Notifications & Events                        │
    │                                                            │
    ├─ Rating Service (Port 8086)                              │
    │   └─ Product Reviews & Ratings                           │
    │                                                            │
    ├─ Search Service (Port 8087)                              │
    │   └─ Product Search & Filtering                          │
    │                                                            │
    ├─ Shipping Service (Port 8088)                            │
    │   └─ Order Shipping & Tracking                           │
    │                                                            │
    ├─ Tax Service (Port 8089)                                 │
    │   └─ Tax Calculation                                     │
    │                                                            │
    ├─ Favourite Service (Port 8090)                           │
    │   └─ User Favorites Management                           │
    │                                                            │
    ├─ Media Service (Port 8091)                               │
    │   └─ File Upload & Management                            │
    │                                                            │
    ├─ Promotion Service (Port 8092)                           │
    │   └─ Promotions & Offers Management                      │
    │                                                            │
    └─ Common Library                                           │
        └─ Shared Utilities & Configurations                   │
```

### Data Flow

Each service has its own database and communicates with others via:
- **Synchronous**: REST API calls
- **Asynchronous**: Kafka message broker

## 📦 Services

| Service | Port | Purpose | Database |
|---------|------|---------|----------|
| **API Gateway** | 8888 | Route & load balance requests | - |
| **Discovery Service** | 8761 | Eureka server for service registration | - |
| **Auth Service** | 8081 | User authentication & JWT tokens | MySQL |
| **Product Service** | 8080 | Product catalog management | MySQL |
| **Order Service** | 8082 | Order processing & management | MySQL |
| **Payment Service** | 8084 | Payment processing | MySQL |
| **Inventory Service** | 8085 | Stock & inventory management | MySQL |
| **Notification Service** | 8083 | Email notifications | MongoDB |
| **Rating Service** | 8086 | Reviews & ratings | MySQL |
| **Search Service** | 8087 | Product search & filtering | Elasticsearch |
| **Shipping Service** | 8088 | Shipping & tracking | MySQL |
| **Tax Service** | 8089 | Tax calculations | MySQL |
| **Favourite Service** | 8090 | User favorites | MySQL |
| **Media Service** | 8091 | File upload & management | PostgreSQL |
| **Promotion Service** | 8092 | Promotions & offers | MySQL |

## 📋 Prerequisites

Before running the project, ensure you have:

- **Java Development Kit (JDK)**: Version 17 or higher
- **Maven**: Version 3.6 or higher (or use `./mvnw`)
- **Docker & Docker Compose**: Latest versions
- **Git**: For version control
- **Databases**:
  - MySQL 8.0+
  - PostgreSQL 13+
  - MongoDB 4.4+
  - Elasticsearch 7.10+
- **Message Broker**:
  - Apache Kafka with Zookeeper

## 🚀 Project Setup

### 1. Clone the Repository

```bash
git clone https://github.com/hoangtien2k3/ecommerce-microservices.git
cd ecommerce-microservices
```

### 2. Install Dependencies

```bash
# Using Maven Wrapper (No Maven installation required)
./mvnw clean install -DskipTests

# OR using Maven (if installed)
mvn clean install -DskipTests
```

### 3. Build All Services

```bash
./mvnw clean package -DskipTests
```

### 4. Configure Environment Variables

Create a `.env` file in the root directory:

```env
SPRING_DATASOURCE_URL=jdbc:mysql://localhost:3306/ecommerce
SPRING_DATASOURCE_USERNAME=root
SPRING_DATASOURCE_PASSWORD=password
SPRING_JPA_HIBERNATE_DDL_AUTO=update

KAFKA_BOOTSTRAP_SERVERS=localhost:9092
KAFKA_GROUP_ID=ecommerce-group

EUREKA_SERVER_URL=http://localhost:8761

MAIL_USERNAME=your-email@gmail.com
MAIL_PASSWORD=your-app-password
```

## 🐳 Docker & Containerization

Each service is containerized with Docker for easy deployment and scaling.

### Docker Images

All services have Dockerfiles that:
- Use Maven multi-stage builds for optimization
- Build the application into a JAR file
- Package it in a lightweight Java runtime container
- Expose the appropriate service port

### Build All Docker Images

```bash
# Build images for all services
docker-compose build

# Or build individual service
cd api-gateway
docker build -t ecommerce/api-gateway:latest .
```

### Run with Docker Compose

The project includes a `docker-compose.yml` for orchestration:

```bash
# Start all services
docker-compose up -d

# View logs
docker-compose logs -f

# Stop services
docker-compose down
```

### Dockerfile Highlights

Each Dockerfile:
- Uses `maven:latest` as build stage
- Runs Maven clean package
- Creates a lightweight runtime image
- Exposes service-specific port
- Sets Spring profiles for containerized environment

Example Dockerfile structure:
```dockerfile
FROM maven:latest
VOLUME /tmp
ARG PROJECT_VERSION=0.0.1
RUN mkdir -p /home/app
WORKDIR /home/app
ENV SPRING_PROFILES_ACTIVE application
COPY ./ .
ADD target/service-0.0.1-SNAPSHOT.jar service-0.0.1-SNAPSHOT.jar
EXPOSE 8XXX
ENTRYPOINT ["java", "-jar", "service-0.0.1-SNAPSHOT.jar"]
CMD ["mvn", "spring-boot:run"]
```

## ▶️ Running Services

### Option 1: Run All Services at Once

```bash
# Using Docker Compose (Recommended)
docker-compose up -d

# Check services status
docker-compose ps
```

### Option 2: Run Individual Services

```bash
# Navigate to service directory
cd product-service

# Build
./mvnw clean package

# Run
./mvnw spring-boot:run

# Or run JAR directly
java -jar target/product-service-0.0.1-SNAPSHOT.jar
```

### Option 3: Run from IDE

1. Open project in IntelliJ IDEA or VS Code
2. Run Spring Boot application configuration for each service
3. Services will start on configured ports

### Verify Services are Running

```bash
# Check Discovery Service (Eureka Dashboard)
curl http://localhost:8761

# Check API Gateway
curl http://localhost:8888/health

# Check individual service
curl http://localhost:8080/products
```

## 📚 API Documentation

### API Gateway Endpoints

All requests should go through the API Gateway (Port 8888):

```
http://localhost:8888
```

### Service Endpoints

#### Auth Service
- `POST /auth/signin` - User login
- `POST /auth/signup` - User registration
- `POST /auth/refresh-token` - Refresh JWT token

#### Product Service
- `GET /products` - List all products
- `GET /products/{id}` - Get product details
- `POST /products` - Create product (Admin only)
- `PUT /products/{id}` - Update product (Admin only)
- `DELETE /products/{id}` - Delete product (Admin only)

#### Order Service
- `GET /orders` - Get user orders
- `POST /orders` - Create order
- `PUT /orders/{id}` - Update order
- `DELETE /orders/{id}` - Cancel order

#### Payment Service
- `POST /payments` - Process payment
- `GET /payments/{orderId}` - Get payment status

#### And more...

Detailed API documentation available in `/docs` folder.

## 🛠️ Technology Stack

### Core Framework
- **Spring Boot 3.x** - Application framework
- **Spring Cloud** - Cloud-native features
- **Spring Data JPA** - ORM & database access
- **Spring WebFlux** - Reactive programming
- **Spring Security** - Authentication & authorization

### Microservices
- **Eureka** - Service discovery
- **Spring Cloud Gateway** - API gateway
- **Ribbon** - Client-side load balancing
- **Hystrix** - Circuit breaker pattern
- **Zuul** - Alternative gateway

### Data Management
- **MySQL 8.0** - Relational database
- **PostgreSQL** - Alternative relational DB
- **MongoDB** - NoSQL database
- **Elasticsearch** - Search & analytics
- **Liquibase** - Database versioning

### Messaging & Events
- **Apache Kafka** - Event streaming
- **Zookeeper** - Kafka coordination
- **RabbitMQ** - Alternative message broker

### Development Tools
- **Maven** - Build tool
- **Docker** - Containerization
- **JUnit 5** - Unit testing
- **Mockito** - Mocking framework
- **SLF4J** - Logging

### Utilities
- **Lombok** - Reduce boilerplate code
- **ModelMapper** - Object mapping
- **Jackson** - JSON serialization

## 📁 Project Structure

```
ecommerce-microservices/
├── api-gateway/              # API Gateway service
├── auth-service/             # Authentication service
├── product-service/          # Product management
├── order-service/            # Order management
├── payment-service/          # Payment processing
├── inventory-service/        # Stock management
├── notification-service/     # Email/notifications
├── rating-service/           # Reviews & ratings
├── search-service/           # Search functionality
├── shipping-service/         # Shipping & tracking
├── tax-service/              # Tax calculations
├── favourite-service/        # User favorites
├── media-service/            # File management
├── promotion-service/        # Promotions & offers
├── discovery-service/        # Eureka server
├── common-lib/               # Shared libraries
├── docker/                   # Docker configurations
│   ├── keycloak/            # Keycloak setup
│   └── postgres/            # PostgreSQL setup
├── docs/                     # Documentation
├── docker-compose.yml        # Compose orchestration
└── pom.xml                   # Root POM file
```

## 🔧 Configuration

### Application Properties

Each service has an `application.yml`:

```yaml
server:
  port: 8080
  servlet:
    context-path: /api/v1

spring:
  application:
    name: product-service
  datasource:
    url: jdbc:mysql://localhost:3306/ecommerce_product
    username: root
    password: password
  jpa:
    hibernate:
      ddl-auto: update
    properties:
      hibernate:
        dialect: org.hibernate.dialect.MySQL8Dialect

  kafka:
    bootstrap-servers: localhost:9092

eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/
  instance:
    prefer-ip-address: true
```

## 📊 Development Timeline

- **Start Date**: November 11, 2024
- **Completion Date**: November 11, 2025
- **Development Duration**: 1 year
- **Architecture**: Microservices with Spring Boot
- **Total Services**: 16 independent microservices

## 🤝 Contributing

We welcome contributions! To contribute:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit changes (`git commit -m 'Add AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 👨‍💻 Author

**Hoang Tien**
- GitHub: [@hoangtien2k3](https://github.com/hoangtien2k3)

## 📞 Support

For support and questions:
- Create an issue on GitHub
- Email: [your-email@example.com]

## 🔗 Resources

- [Spring Boot Documentation](https://spring.io/projects/spring-boot)
- [Spring Cloud Documentation](https://spring.io/projects/spring-cloud)
- [Docker Documentation](https://docs.docker.com/)
- [Apache Kafka Documentation](https://kafka.apache.org/documentation/)
- [Eureka Documentation](https://github.com/Netflix/eureka)

---

**Last Updated**: May 11, 2026

Made with ❤️ by the Ecommerce Microservices Team

#### 2. Build the project:

```bash
  # Using Maven
  mvn clean install
  
  # Using Gradle
  gradle build
```

#### 3. Configure the database:

- Update `application.properties` or `application.yml` with your database connection details.

#### 4. Run the application:

```bash
  # Using Maven
  mvn spring-boot:run
  
  # Using Gradle
  gradle bootRun
```

## Demo
![1715441188385](https://github.com/user-attachments/assets/ea07616a-5404-4ccd-bab0-b472b67a061a)

## Technologies Used

- `Java`: The primary programming language.
- `Spring Boot`: Framework for building Java-based enterprise applications.
- `Maven/Gradle`: Build tools for managing dependencies and building the project.
- `Database`: Choose and specify the database system used (e.g., MySQL, PostgreSQL).
- `Other Dependencies`: List any additional dependencies or libraries used.

## API Documentation

Document the API endpoints and their functionalities. You can use tools like `Swagger` for
automated `API documentation`.

## Star History

<a href="https://star-history.com/#hoangtien2k3/ecommerce-microservices&Date">
 <picture>
   <source media="(prefers-color-scheme: dark)" srcset="https://api.star-history.com/svg?repos=hoangtien2k3/ecommerce-microservices&type=Date&theme=dark" />
   <source media="(prefers-color-scheme: light)" srcset="https://api.star-history.com/svg?repos=hoangtien2k3/ecommerce-microservices&type=Date" />
   <img alt="Star History Chart" src="https://api.star-history.com/svg?repos=hoangtien2k3/ecommerce-microservices&type=Date" />
 </picture>
</a>

## Contributing

If you would like to contribute to the development of this project, please follow our contribution guidelines.

![Alt](https://repobeats.axiom.co/api/embed/1897bc523b54b43aefb19c65195f32377f8aab85.svg "Repobeats analytics image")

## Stargazers

[![Stargazers repo roster for @hoangtien2k3/ecommerce-microservices](http://reporoster.com/stars/dark/hoangtien2k3/ecommerce-microservices)](https://github.com/hoangtien2k3/ecommerce-microservices/stargazers)

## Forkers

[![Forkers repo roster for @hoangtien2k3/ecommerce-microservices](http://reporoster.com/forks/dark/hoangtien2k3/ecommerce-microservices)](https://github.com/hoangtien2k3/ecommerce-microservices/network/members)

## Contributors ✨

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
<!-- prettier-ignore-start -->
<!-- markdownlint-disable -->
<table>
  <tr>
    <td align="center"><a href="https://www.linkedin.com/in/hoangtien2k3/"><img src="https://avatars.githubusercontent.com/u/122768076?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Hoàng Anh Tiến</b></sub></a><br /><a href="https://github.com/hoangtien2k3/news-app/commits?author=hoangtien2k3" title="Code">💻</a> <a href="#maintenance-hoangtien2k3" title="Maintenance">🚧</a> <a href="#ideas-hoangtien2k3" title="Ideas, Planning, & Feedback">🤔</a> <a href="#design-hoangtien2k3" title="Design">🎨</a> <a href="https://github.com/hoangtien2k3/news-app/issues?q=author%hoangtien2k3" title="Bug reports">🐛</a></td>
     <td align="center"><a href="https://github.com/hoangchungk53qx1"><img src="https://avatars.githubusercontent.com/u/52132635?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Hoàng Anh Chung</b></sub></a><br /><a href="https://github.com/hoangtien2k3/news-app/commits?author=hoangtien2k3" title="Code">🤔</a> <a href="#design-hoangtien2k3" title="Design">🎨</a> <a href="https://github.com/hoangtien2k3/news-app/issues?q=author%hoangtien2k3" title="Bug reports">🐛</a></td>
  </tr>

</table>

<!-- markdownlint-restore -->
<!-- prettier-ignore-end -->
<!-- ALL-CONTRIBUTORS-LIST:END -->

# Sponsors

Become a sponsor and get your logo in our README on Github with a link to your site. [[Become a sponsor](https://opencollective.com/ecommerce-microservices#sponsor)]

<a href="https://opencollective.com/ecommerce-microservices/sponsor/0/website" target="_blank"><img src="https://opencollective.com/ecommerce-microservices/sponsor/0/avatar.svg"></a>
<a href="https://opencollective.com/ecommerce-microservices/sponsor/1/website" target="_blank"><img src="https://opencollective.com/ecommerce-microservices/sponsor/1/avatar.svg"></a>
<a href="https://opencollective.com/ecommerce-microservices/sponsor/2/website" target="_blank"><img src="https://opencollective.com/ecommerce-microservices/sponsor/2/avatar.svg"></a>
<a href="https://opencollective.com/ecommerce-microservices/sponsor/3/website" target="_blank"><img src="https://opencollective.com/ecommerce-microservices/sponsor/3/avatar.svg"></a>
<a href="https://opencollective.com/ecommerce-microservices/sponsor/4/website" target="_blank"><img src="https://opencollective.com/ecommerce-microservices/sponsor/4/avatar.svg"></a>

## License

This project is licensed under the [`MIT License`](LICENSE).

```text
MIT License
Copyright (c) 2024 Hoàng Anh Tiến
```
