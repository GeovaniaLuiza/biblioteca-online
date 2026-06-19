# API Contract: Borrows

**Base URL**: `/api/v1/borrows`

---

## POST /api/v1/borrows

Borrow a book. Requires authentication.

**Headers**: `Authorization: Bearer <token>`

**Request Body**:
```json
{
  "bookId": 1
}
```

**Validation Rules**:
- `bookId`: integer, required, must reference existing book

**Success Response** (201 Created):
```json
{
  "id": 1,
  "book": {
    "id": 1,
    "title": "The Great Gatsby"
  },
  "borrowDate": "2026-06-19T00:00:00.000Z",
  "dueDate": "2026-07-03T00:00:00.000Z",
  "status": "ACTIVE"
}
```

**Error Responses**:
- 400: `{ "error": "Book is not available" }`
- 400: `{ "error": "Maximum active borrows reached (5)" }`
- 400: `{ "error": "Cannot borrow: you have overdue books" }`
- 404: `{ "error": "Book not found" }`
- 401: `{ "error": "Authentication required" }`

---

## GET /api/v1/borrows

List current user's active borrows. Requires authentication.

**Headers**: `Authorization: Bearer <token>`

**Query Parameters**:
- `status` (optional): `ACTIVE` | `RETURNED` | `OVERDUE` — filter by status
- `page` (optional): integer, default 1
- `limit` (optional): integer, default 20, max 100

**Success Response** (200 OK):
```json
{
  "borrows": [
    {
      "id": 1,
      "book": {
        "id": 1,
        "title": "The Great Gatsby"
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

---

## POST /api/v1/borrows/:id/return

Return a borrowed book. Requires authentication. User can return their own borrows; admin can return any.

**Headers**: `Authorization: Bearer <token>`

**Success Response** (200 OK):
```json
{
  "id": 1,
  "book": {
    "id": 1,
    "title": "The Great Gatsby"
  },
  "borrowDate": "2026-06-19T00:00:00.000Z",
  "dueDate": "2026-07-03T00:00:00.000Z",
  "returnDate": "2026-06-25T00:00:00.000Z",
  "status": "RETURNED"
}
```

**Error Responses**:
- 400: `{ "error": "Borrow is already returned" }`
- 403: `{ "error": "You can only return your own borrows" }`
- 404: `{ "error": "Borrow record not found" }`
