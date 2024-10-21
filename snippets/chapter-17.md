17 사용자 중심 API 디자인
=====

## 17.1. 일관성

- `customer`, `order`, `product` 도메인을 갖는 API 예시

```
GET /customers
POST /customers
GET /customers/{customerId}
PUT /customers/{customerId}
DELETE /customers/{customerId}
GET /customers/{customerId}/orders
POST /customers/{customerId}/orders
GET /orders/{orderId}
PUT /orders/{orderId}
DELETE /orders/{orderId}
POST /orders/{orderId}/cancel
GET /products
GET /products/{productId}
GET /products/{productId}/reviews
POST /products/{productId}/reviews
```

- 중첩된 리소스

```
/customers/{customerId}/addresses
/orders/{orderId}/items
```

- 중첩된 URL의 좋은 예와 나쁜 예

```
// 좋음
/orders/{orderId}/items

// 나쁨
/customers/{customerId}/orders/{orderId}/items/{itemId}/variants
```

- 쿼리 매개변수 사용하기

```
GET /products?category=electronics&sort=price&page=2&limit=20
```

- 유사한 구조를 갖는 두 리소스

```
GET /users/{userId}/profile
GET /companies/{companyId}/profile
```

- 일관성 있는 리소스 구조를 갖는 e-커머스 API 예시

```
/users
/users/{userId}
/users/{userId}/orders
/users/{userId}/addresses

/products
/products/{productId}
/products/{productId}/reviews

/orders
/orders/{orderId}
/orders/{orderId}/items
/orders/{orderId}/shipments

/categories
/categories/{categoryId}
/categories/{categoryId}/products
```

- 성공 및 에러 응답 예시

```json
// 성공 응답
{
  "status": "success",
  "data": {
    "id": 123,
    "name": "John Doe",
    "email": "john@example.com"
  }
}

// 에러 응답
{
  "status": "error",
  "data": {
    "message": "User not found",
    "code": "NOT_FOUND"
  }
}
```

- 서로 다른 리소스에서 `creatdAt`과 `updatedAt` 사용하기

```json
// User
{
  "id": 123,
  "createdAt": "2023-07-01T12:00:00Z",
  "updatedAt": "2023-07-02T14:30:00Z",
  "name": "John Doe"
}

// Product
{
  "id": 456,
  "createdAt": "2023-06-15T09:00:00Z",
  "updatedAt": "2023-06-16T11:45:00Z",
  "name": "Smartphone X"
}
```

- 서로 다른 데이터 필드에 대한 ISO 8601 형식 유지하기

```json
{
  "createdAt": "2023-07-01T12:00:00Z",
  "updatedAt": "2023-07-02T14:30:00Z",
  "scheduledFor": "2023-07-10T09:00:00+02:00"
}
```

- 페이지네이션 정보를 포함한 메타데이터

```json
{
  "status": "success",
  "data": [
    // 아이템 배열
  ],
  "metadata": {
    "page": 2,
    "perPage": 20,
    "totalItems": 157,
    "totalPages": 8
  },
  "requestId": "req_abc123"
}
```

## 17.2. 에러 핸들링

- 상세한 정보를 담은 명확한 에러 페이로드

```json
{
  "status": "error",
  "message": "Invalid email format",
  "code": "INVALID_EMAIL",
  "details": "Provided email 'johndoe@example' is missing a domain"
}
```

- 에러 응답 구조

```json
{
  "status": "error",
  "message": "string",
  "code": "string",
  "details": "string",
  "requestId": "string"
}
```

- 잔액 부족에 대한 고유한 에러 코드

```json
{
  "status": "error",
  "message": "Insufficient funds",
  "code": "INSUFFICIENT_FUNDS",
  "details": "Your account balance is $50, but the transaction requires $100"
}
```

- 2개의 다른 폴 필드에 대한 검증 에러

```json
{
  "status": "error",
  "message": "Validation failed",
  "code": "VALIDATION_ERROR",
  "errors": [
    {
      "field": "email",
      "message": "Invalid email format"
    },
    {
      "field": "password",
      "message": "Password must be at least 8 characters long"
    }
  ]
}
```

## 17.4. 버저닝

- URL 버저닝 예시

```
GET /v1/users/123
GET /v2/users/123
```

- 쿼리 매개변수 버저닝 예시

```
GET /users/123?version=1
GET /users/123?version=2
```

- 헤더 버저닝 예시

```
GET /users/123
Header: API-Version: 1
```

- GET /v1/products

```json
// GET /v1/products
// 응답:
{
  "data": [
    { "id": 1, "name": "Product A" },
    { "id": 2, "name": "Product B" }
  ]
}

// GET /v2/products
// 응답:
{
  "products": [
    { "productId": 1, "productName": "Product A" },
    { "productId": 2, "productName": "Product B" }
  ]
}
```

## 17.5. 보안

- Node.js에서 JWT를 사용한 간단한 인증 엔드포인트

```javascript
// 인증 엔드포인트
app.post("/login", (req, res) => {
  const { username, password } = req.body;

  // 사용자가 존재하고, 비밀번호가 올바른지 확인한다.
  const user = findUserByUsername(username);
  if (
    user &&
    verifyPassword(
      password,
      user.hashedPassword,
    )
  ) {
    // JWT 토큰을 생성하고 전송한다.
    const token = generateJWT(user);
    res.json({ token });
  } else {
    res
      .status(401)
      .json({ error: "Invalid credentials" });
  }
});
```

- Node.js에서 허가를 사용해 보호된 라우트

```javascript
// 허가를 필요로 하는 보호된 라우트
app.get(
  "/api/protected-resource",
  authenticateToken,
  (req, res) => {
    // 사용자가 필요한 권한을 갖고 있는지 확인한다.
    if (req.user.role === "admin") {
      // 보호된 리소스에 대한 접근을 제공한다.
      res.json({
        data: "This is sensitive information",
      });
    } else {
      res
        .status(403)
        .json({
          error: "Insufficient permissions",
        });
    }
  },
);

// JWT 토큰을 인증하기 위한 미들웨어
function authenticateToken(req, res, next) {
  const token = req.headers["authorization"];

  if (!token)
    return res
      .status(401)
      .json({ error: "No token provided" });
  
  verifyJWT(token, (err, user) => {
    if (err)
      return res
        .status(403)
        .json({ error: "Invalid token" });
    req.user = user;
    next();
  });
}
```

- Express.js에서 보안 헤더 설정하기

```javascript
app.use((req, res, next) => {
  res.setHeader(
    "Content-Security-Policy",
    "default-src 'self';",
  );

  res.setHeader(
    "Content-Type",
    "application/json",
  );

  res.setHeader(
    "Strict-Transport-Security",
    "max-age=63072000; includeSubDomains; preload",
  );

  // ...
  // ...
});
```

- Node.js에서의 입력 검증 예시

```javascript
app.post("/register", (req, res) => {
  const { username, email, password } =
    req.body;

  // 입력 검증

  if (
    !isValidUsername(username) ||
    !isValidEmail(email) ||
    !isStrongPassword(password)
  ) {
    return res
      .status(400)
      .json({ error: "Invalid input" });
  }

  // 입력 안정성 검사

  const sanitizedUsername =
    sanitizeString(username);
  const sanitizedEmail = sanitizeEmail(email);

  // 사용자 등록을 진행한다.
  // ...
});

function isValidUsername(username) {
  return /^[a-zA-Z0-9_]{3,20}$/.test(username);
}

function isValidEmail(email) {
  return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(
    email,
  );
}

function isStrongPassword(password) {
  return (
    password.length >= 8 &&
      /[A-Z]/.test(password) &&
      /[a-z]/.test(password) &&
      /[0-9]/.test(password)
  );
}
```

- Node/Express.js 애플리케이션에서 CORS를 설정하는 간단한 예시

```javascript
const cors = require("cors");

// CORS 옵션을 설정한다.
const corsOptions = {
  origin: "https://example.com", // 이 출처는 허용한다.
  optionsSuccessStatus: 200, // 합법적인 지원
};

app.use(cors(corsOptions));

app.get("/api/data", (req, res) => {
  res.json({
    message:
    "This is data accessible from example.com.",
  });
});
```

- Node.js에서의 레이트 제한 예시

```javascript
const rateLimit = require("express-rate-limit");

const apiLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15분
  max: 100, // 각 IP에 대해 windowMS 당 요청을 100개로 제한한다.
  message: 
    "Too many requests from this IP, try again later",
  });

  // 모든 요청에 대해 레이트 제한을 적용한다.
  app.use(apiLimiter);
```
