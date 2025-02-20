# 📝 API Documentation

## Base URL

All endpoints are accessible through the API Gateway:

```
http://localhost:8888/api/v1
```

## Authentication

All requests (except auth endpoints) require a JWT token in the header:

```
Authorization: Bearer <your_jwt_token>
```

## Services and Endpoints

### 1. Auth Service (Port 8081)

Authentication and user management.

#### Sign Up
```
POST /auth/signup
Content-Type: application/json

{
  "username": "john_doe",
  "email": "john@example.com",
  "password": "securePassword123"
}

Response: 201 Created
{
  "id": "123",
  "username": "john_doe",
  "email": "john@example.com",
  "roles": ["USER"]
}
```

#### Sign In
```
POST /auth/signin
Content-Type: application/json

{
  "email": "john@example.com",
  "password": "securePassword123"
}

Response: 200 OK
{
  "token": "eyJhbGciOiJIUzUxMiJ9...",
  "type": "Bearer",
  "refreshToken": "...",
  "expiresIn": 86400
}
```

#### Refresh Token
```
POST /auth/refresh-token
Content-Type: application/json

{
  "refreshToken": "..."
}

Response: 200 OK
{
  "token": "eyJhbGciOiJIUzUxMiJ9...",
  "refreshToken": "...",
  "expiresIn": 86400
}
```

---

### 2. Product Service (Port 8080)

Product catalog management.

#### List All Products
```
GET /products?page=0&size=10
Authorization: Bearer <token>

Response: 200 OK
{
  "content": [
    {
      "id": "1",
      "name": "Laptop",
      "description": "High-performance laptop",
      "price": 999.99,
      "categoryId": "1",
      "stock": 50
    }
  ],
  "pageNumber": 0,
  "pageSize": 10,
  "totalElements": 100
}
```

#### Get Product by ID
```
GET /products/{productId}
Authorization: Bearer <token>

Response: 200 OK
{
  "id": "1",
  "name": "Laptop",
  "description": "High-performance laptop",
  "price": 999.99,
  "categoryId": "1",
  "stock": 50,
  "rating": 4.5,
  "reviewCount": 250
}
```

#### Create Product (Admin)
```
POST /products
Authorization: Bearer <token>
Content-Type: application/json

{
  "name": "New Product",
  "description": "Product description",
  "price": 49.99,
  "categoryId": "1",
  "stock": 100
}

Response: 201 Created
{
  "id": "123",
  "name": "New Product",
  "description": "Product description",
  "price": 49.99,
  "categoryId": "1",
  "stock": 100
}
```

#### Update Product (Admin)
```
PUT /products/{productId}
Authorization: Bearer <token>
Content-Type: application/json

{
  "name": "Updated Product",
  "price": 59.99,
  "stock": 95
}

Response: 200 OK
```

#### Delete Product (Admin)
```
DELETE /products/{productId}
Authorization: Bearer <token>

Response: 204 No Content
```

---

### 3. Order Service (Port 8082)

Order and cart management.

#### Get User Orders
```
GET /orders
Authorization: Bearer <token>

Response: 200 OK
{
  "content": [
    {
      "id": "1",
      "userId": "123",
      "status": "COMPLETED",
      "totalPrice": 1299.98,
      "items": [
        {
          "productId": "1",
          "quantity": 1,
          "price": 999.99
        }
      ],
      "createdAt": "2024-01-15T10:30:00Z"
    }
  ]
}
```

#### Get Order by ID
```
GET /orders/{orderId}
Authorization: Bearer <token>

Response: 200 OK
{
  "id": "1",
  "userId": "123",
  "status": "COMPLETED",
  "totalPrice": 1299.98,
  "items": [...],
  "shippingAddress": "123 Main St",
  "createdAt": "2024-01-15T10:30:00Z"
}
```

#### Create Order
```
POST /orders
Authorization: Bearer <token>
Content-Type: application/json

{
  "items": [
    {
      "productId": "1",
      "quantity": 1
    }
  ],
  "shippingAddress": "123 Main St",
  "paymentMethod": "CREDIT_CARD"
}

Response: 201 Created
{
  "id": "new-order-id",
  "status": "PENDING",
  "totalPrice": 999.99
}
```

#### Update Order
```
PUT /orders/{orderId}
Authorization: Bearer <token>
Content-Type: application/json

{
  "status": "SHIPPED"
}

Response: 200 OK
```

#### Cancel Order
```
DELETE /orders/{orderId}
Authorization: Bearer <token>

Response: 204 No Content
```

---

### 4. Payment Service (Port 8084)

Payment processing.

#### Process Payment
```
POST /payments
Authorization: Bearer <token>
Content-Type: application/json

{
  "orderId": "1",
  "amount": 999.99,
  "paymentMethod": "CREDIT_CARD",
  "cardNumber": "4532****1111",
  "expiryDate": "12/25",
  "cvv": "123"
}

Response: 201 Created
{
  "id": "pay-123",
  "orderId": "1",
  "status": "COMPLETED",
  "amount": 999.99,
  "transactionId": "txn_123"
}
```

#### Get Payment Status
```
GET /payments/{orderId}
Authorization: Bearer <token>

Response: 200 OK
{
  "paymentId": "pay-123",
  "orderId": "1",
  "status": "COMPLETED",
  "amount": 999.99,
  "processedAt": "2024-01-15T10:31:00Z"
}
```

---

### 5. Inventory Service (Port 8085)

Stock management.

#### Get Inventory
```
GET /inventory/{productId}
Authorization: Bearer <token>

Response: 200 OK
{
  "productId": "1",
  "availableStock": 50,
  "reservedStock": 5,
  "totalStock": 55
}
```

#### Update Inventory
```
PUT /inventory/{productId}
Authorization: Bearer <token>
Content-Type: application/json

{
  "quantity": 45,
  "action": "RESERVE"
}

Response: 200 OK
```

---

### 6. Rating Service (Port 8086)

Product reviews and ratings.

#### Get Product Reviews
```
GET /products/{productId}/reviews
Authorization: Bearer <token>

Response: 200 OK
{
  "content": [
    {
      "id": "1",
      "productId": "1",
      "userId": "123",
      "rating": 5,
      "title": "Excellent product!",
      "comment": "Very satisfied with this purchase",
      "createdAt": "2024-01-15T10:30:00Z"
    }
  ]
}
```

#### Add Review
```
POST /products/{productId}/reviews
Authorization: Bearer <token>
Content-Type: application/json

{
  "rating": 5,
  "title": "Great product!",
  "comment": "Highly recommended"
}

Response: 201 Created
```

---

### 7. Search Service (Port 8087)

Product search and filtering.

#### Search Products
```
GET /search?query=laptop&category=electronics&minPrice=500&maxPrice=1500&page=0&size=10
Authorization: Bearer <token>

Response: 200 OK
{
  "content": [
    {
      "id": "1",
      "name": "Laptop",
      "price": 999.99,
      "rating": 4.5,
      "relevanceScore": 0.95
    }
  ],
  "totalElements": 45
}
```

---

### 8. Shipping Service (Port 8088)

Order shipping and tracking.

#### Get Shipment Status
```
GET /shipments/{orderId}
Authorization: Bearer <token>

Response: 200 OK
{
  "shipmentId": "ship-123",
  "orderId": "1",
  "status": "IN_TRANSIT",
  "trackingNumber": "TRACK123456",
  "carrier": "FedEx",
  "estimatedDelivery": "2024-01-18"
}
```

---

### 9. Tax Service (Port 8089)

Tax calculations.

#### Calculate Tax
```
POST /taxes/calculate
Authorization: Bearer <token>
Content-Type: application/json

{
  "amount": 999.99,
  "state": "CA",
  "country": "US"
}

Response: 200 OK
{
  "amount": 999.99,
  "taxRate": 0.0825,
  "taxAmount": 82.49,
  "total": 1082.48
}
```

---

### 10. Favourite Service (Port 8090)

User favorites management.

#### Get User Favorites
```
GET /favorites
Authorization: Bearer <token>

Response: 200 OK
{
  "content": [
    {
      "id": "1",
      "productId": "1",
      "productName": "Laptop",
      "addedAt": "2024-01-15T10:30:00Z"
    }
  ]
}
```

#### Add to Favorites
```
POST /favorites/{productId}
Authorization: Bearer <token>

Response: 201 Created
```

#### Remove from Favorites
```
DELETE /favorites/{productId}
Authorization: Bearer <token>

Response: 204 No Content
```

---

## Error Handling

All services return consistent error responses:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input",
    "details": [
      {
        "field": "email",
        "message": "Invalid email format"
      }
    ]
  },
  "timestamp": "2024-01-15T10:30:00Z"
}
```

### Error Codes

| Code | HTTP Status | Description |
|------|------------|-------------|
| VALIDATION_ERROR | 400 | Input validation failed |
| UNAUTHORIZED | 401 | Missing or invalid token |
| FORBIDDEN | 403 | Insufficient permissions |
| NOT_FOUND | 404 | Resource not found |
| CONFLICT | 409 | Resource already exists |
| INTERNAL_ERROR | 500 | Server error |

---

## Rate Limiting

API Gateway implements rate limiting:

- **Per User**: 1000 requests/hour
- **Per API Key**: 10000 requests/hour

Headers returned:
```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1610696400
```

---

**Last Updated**: May 11, 2026