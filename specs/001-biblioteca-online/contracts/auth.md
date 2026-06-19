# API Contract: Authentication

**Base URL**: `/api/v1/auth`

---

## POST /api/v1/auth/register

Register a new user account.

**Request Body**:
```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "securePassword123"
}
```

**Validation Rules**:
- `name`: string, 1–100 chars, required
- `email`: string, valid email format, max 255 chars, required, unique
- `password`: string, 8–128 chars, required

**Success Response** (201 Created):
```json
{
  "user": {
    "id": 1,
    "name": "John Doe",
    "email": "john@example.com",
    "role": "USER"
  },
  "token": "eyJhbGciOiJIUzI1NiIs..."
}
```

**Error Responses**:
- 400: `{ "error": "Validation failed", "details": [...] }`
- 409: `{ "error": "Email already registered" }`

---

## POST /api/v1/auth/login

Authenticate with email and password.

**Request Body**:
```json
{
  "email": "john@example.com",
  "password": "securePassword123"
}
```

**Success Response** (200 OK):
```json
{
  "user": {
    "id": 1,
    "name": "John Doe",
    "email": "john@example.com",
    "role": "USER"
  },
  "token": "eyJhbGciOiJIUzI1NiIs..."
}
```

**Error Responses**:
- 401: `{ "error": "Invalid email or password" }`

---

## GET /api/v1/auth/me

Get the currently authenticated user's profile.

**Headers**: `Authorization: Bearer <token>`

**Success Response** (200 OK):
```json
{
  "id": 1,
  "name": "John Doe",
  "email": "john@example.com",
  "role": "USER"
}
```

**Error Responses**:
- 401: `{ "error": "Authentication required" }`
