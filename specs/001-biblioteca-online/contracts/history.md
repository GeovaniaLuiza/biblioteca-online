# API Contract: History

**Base URL**: `/api/v1/history`

---

## GET /api/v1/history/me

Get the authenticated user's borrowing history. Requires authentication.

**Headers**: `Authorization: Bearer <token>`

**Query Parameters**:
- `page` (optional): integer, default 1
- `limit` (optional): integer, default 20, max 100
- `status` (optional): `ACTIVE` | `RETURNED` | `OVERDUE`

**Success Response** (200 OK):
```json
{
  "history": [
    {
      "id": 1,
      "book": {
        "id": 1,
        "title": "The Great Gatsby",
        "author": "F. Scott Fitzgerald"
      },
      "borrowDate": "2026-06-19T00:00:00.000Z",
      "dueDate": "2026-07-03T00:00:00.000Z",
      "returnDate": "2026-06-25T00:00:00.000Z",
      "status": "RETURNED"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 1,
    "totalPages": 1
  }
}
```

---

## GET /api/v1/history/users/:userId

Get a specific user's borrowing history. Admin only.

**Headers**: `Authorization: Bearer <token>`

**Query Parameters**: Same as `/history/me`

**Success Response** (200 OK): Same structure as `/history/me`

**Error Responses**:
- 403: `{ "error": "Admin privileges required" }`
- 404: `{ "error": "User not found" }`

---

## GET /api/v1/history/all

Get all borrowing transactions across all users. Admin only.

**Headers**: `Authorization: Bearer <token>`

**Query Parameters**:
- `page` (optional): integer, default 1
- `limit` (optional): integer, default 20, max 100
- `userId` (optional): integer — filter by specific user
- `status` (optional): `ACTIVE` | `RETURNED` | `OVERDUE`

**Success Response** (200 OK):
```json
{
  "history": [
    {
      "id": 1,
      "user": {
        "id": 1,
        "name": "John Doe",
        "email": "john@example.com"
      },
      "book": {
        "id": 1,
        "title": "The Great Gatsby",
        "author": "F. Scott Fitzgerald"
      },
      "borrowDate": "2026-06-19T00:00:00.000Z",
      "dueDate": "2026-07-03T00:00:00.000Z",
      "returnDate": null,
      "status": "ACTIVE"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 1,
    "totalPages": 1
  }
}
```

**Error Responses**:
- 403: `{ "error": "Admin privileges required" }`
