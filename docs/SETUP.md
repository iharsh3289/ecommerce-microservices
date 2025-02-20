# 🔧 Environment Setup Guide

## Overview

This guide explains how to properly configure the ecommerce microservices project using environment variables. The project uses a `.env.example` file as a template for local development.

## Getting Started

### 1. Create Your Local Environment File

```bash
# Copy the example environment file
cp .env.example .env

# Verify the file was created
ls -la .env
```

### 2. Edit the `.env` File

Open `.env` in your editor and update the values according to your local setup:

```bash
# Using your preferred editor
nano .env
# or
vim .env
# or use VS Code
code .env
```

## Configuration Sections

### Common Configuration

```env
SPRING_PROFILES_ACTIVE=dev          # Application profile (dev, test, prod)
APP_NAME=ecommerce-microservices    # Application name
ENVIRONMENT=development              # Environment type
```

### Database Configuration

**For MySQL (Default):**

```env
SPRING_DATASOURCE_URL=jdbc:mysql://localhost:3306/ecommerce
SPRING_DATASOURCE_USERNAME=root
SPRING_DATASOURCE_PASSWORD=root123
```

**For PostgreSQL (Alternative):**

```env
POSTGRES_HOST=localhost
POSTGRES_PORT=5432
POSTGRES_DB=ecommerce
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres123
```

**For MongoDB:**

```env
SPRING_DATA_MONGODB_URI=mongodb://localhost:27017/ecommerce
SPRING_DATA_MONGODB_USERNAME=mongouser
SPRING_DATA_MONGODB_PASSWORD=mongopass
```

### Kafka Configuration

```env
KAFKA_BOOTSTRAP_SERVERS=localhost:9092
KAFKA_GROUP_ID=ecommerce-group
KAFKA_TOPIC_REPLICATION_FACTOR=1
```

### JWT Security

```env
JWT_SECRET_KEY=your-super-secret-jwt-key-change-this-in-production
JWT_EXPIRATION_MS=86400000           # 24 hours
JWT_REFRESH_EXPIRATION_MS=604800000  # 7 days
```

### Email Configuration

```env
MAIL_HOST=smtp.gmail.com
MAIL_PORT=587
MAIL_USERNAME=your-email@gmail.com
MAIL_PASSWORD=your-app-password      # Use app-specific password for Gmail
MAIL_FROM=noreply@ecommerce.com
```

### Service URLs

For **local development** (using localhost):

```env
SERVICE_AUTH_URL=http://localhost:8081
SERVICE_PRODUCT_URL=http://localhost:8080
SERVICE_ORDER_URL=http://localhost:8082
```

For **Docker Compose** (using service names):

```env
SERVICE_AUTH_URL=http://auth-service:8081
SERVICE_PRODUCT_URL=http://product-service:8080
SERVICE_ORDER_URL=http://order-service:8082
```

## Running with Different Configurations

### Option 1: Local Development

```bash
# Create .env file with localhost URLs
cp .env.example .env

# Update relevant sections to use localhost
SPRING_DATASOURCE_URL=jdbc:mysql://localhost:3306/ecommerce
KAFKA_BOOTSTRAP_SERVERS=localhost:9092

# Run services individually
./mvnw spring-boot:run
```

### Option 2: Docker Compose

```bash
# Copy example file
cp .env.example .env

# Update to use Docker service names
SPRING_DATASOURCE_URL=jdbc:mysql://mysql:3306/ecommerce
KAFKA_BOOTSTRAP_SERVERS=kafka:29092

# Start with Docker Compose
docker-compose up -d
```

### Option 3: Production Deployment

For production, **never use .env files**. Instead:

```bash
# Use environment variables directly or a secret management system
export JWT_SECRET_KEY="your-production-secret-key"
export SPRING_DATASOURCE_PASSWORD="secure-password"

# Or use AWS Secrets Manager, HashiCorp Vault, etc.
```

## Gmail Setup (For Email Notifications)

If using Gmail for sending notifications:

### Step 1: Enable 2-Factor Authentication
- Go to Google Account → Security
- Enable 2-Step Verification

### Step 2: Generate App Password
- Go to https://myaccount.google.com/apppasswords
- Select Mail and Windows Computer
- Generate a new app password

### Step 3: Update .env

```env
MAIL_USERNAME=your-email@gmail.com
MAIL_PASSWORD=<16-character-app-password>  # Copy from Step 2
```

## Service Ports Reference

| Service | Port | Default |
|---------|------|---------|
| API Gateway | 8888 | ✅ |
| Product Service | 8080 | ✅ |
| Auth Service | 8081 | ✅ |
| Order Service | 8082 | ✅ |
| Notification Service | 8083 | ✅ |
| Payment Service | 8084 | ✅ |
| Inventory Service | 8085 | ✅ |
| Rating Service | 8086 | ✅ |
| Search Service | 8087 | ✅ |
| Shipping Service | 8088 | ✅ |
| Tax Service | 8089 | ✅ |
| Favourite Service | 8090 | ✅ |
| Media Service | 8091 | ✅ |
| Promotion Service | 8092 | ✅ |
| Discovery Service | 8761 | ✅ |

## Port Conflicts

If you get "port already in use" error:

```bash
# Find what's using the port (macOS/Linux)
lsof -i :8080

# Kill the process (macOS/Linux)
kill -9 <PID>

# For Windows
netstat -ano | findstr :8080
taskkill /PID <PID> /F
```

Or update the port in `.env`:

```env
PRODUCT_SERVICE_PORT=9000  # Use 9000 instead of 8080
```

## Secrets Management Best Practices

### ✅ DO

- ✅ Use `.env.example` as a template
- ✅ Keep `.env` in `.gitignore`
- ✅ Use strong, unique passwords in production
- ✅ Rotate secrets regularly
- ✅ Use environment variables for secrets
- ✅ Use secret management systems in production (AWS Secrets Manager, Vault, etc.)
- ✅ Set proper file permissions: `chmod 600 .env`

### ❌ DON'T

- ❌ Commit `.env` file to Git
- ❌ Use default passwords in production
- ❌ Share `.env` files via email or chat
- ❌ Store secrets in code or config files
- ❌ Use the same secrets across environments
- ❌ Log or expose secrets in error messages

## File Permissions

Protect your `.env` file:

```bash
# Set proper permissions (owner can read/write only)
chmod 600 .env

# Verify permissions
ls -la .env
# Output should show: -rw-------
```

## Troubleshooting

### Issue: Services can't connect to database

**Solution**: Check database configuration in `.env`

```bash
# Test MySQL connection
mysql -h localhost -u root -proot123 -e "SELECT 1"

# Test connection string format
# Should be: jdbc:mysql://HOST:PORT/DATABASE
```

### Issue: Kafka connection timeout

**Solution**: Verify Kafka is running

```bash
# Check if Kafka is running
docker-compose ps kafka

# Or check port
lsof -i :9092
```

### Issue: JWT token errors

**Solution**: Verify JWT_SECRET_KEY is set and consistent

```bash
# Check if variable is set
echo $JWT_SECRET_KEY

# Make sure it's long enough (at least 32 characters)
```

### Issue: Email not sending

**Solution**: Check email configuration

```bash
# Test Gmail credentials
# 1. Verify MAIL_USERNAME and MAIL_PASSWORD are correct
# 2. Check if 2-FA is enabled
# 3. Verify app password is used (not account password)
```

## Environment-Specific Configurations

### Development

```env
SPRING_PROFILES_ACTIVE=dev
LOGGING_LEVEL_ROOT=DEBUG
SPRING_JPA_SHOW_SQL=true
```

### Testing

```env
SPRING_PROFILES_ACTIVE=test
LOGGING_LEVEL_ROOT=INFO
H2_CONSOLE_ENABLED=true
```

### Production

```env
SPRING_PROFILES_ACTIVE=prod
LOGGING_LEVEL_ROOT=WARN
SPRING_JPA_SHOW_SQL=false
```

## Quick Start Commands

```bash
# 1. Copy example file
cp .env.example .env

# 2. Edit with your values
nano .env

# 3. Set proper permissions
chmod 600 .env

# 4. Verify configuration
cat .env | grep -E "DATASOURCE|KAFKA|JWT"

# 5. Run application
./mvnw spring-boot:run
```

## Additional Resources

- [Spring Boot Configuration Documentation](https://spring.io/projects/spring-boot)
- [Kafka Configuration Guide](https://kafka.apache.org/documentation/)
- [JWT Best Practices](https://tools.ietf.org/html/rfc7519)
- [Docker Environment Variables](https://docs.docker.com/compose/environment-variables/)

---

**Created**: February 20, 2025
**Last Updated**: May 11, 2026