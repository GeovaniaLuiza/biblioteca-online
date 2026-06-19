# API Contract: Books

**Base URL**: `/api/v1/books`

---

## GET /api/v1/books

List all books with optional search. Requires authentication.

**Query Parameters**:
- `search` (optional): string — searches title and author
- `status` (optional): `AVAILABLE` | `BORROWED` — filter by availability
- `page` (optional): integer, default 1
- `limit` (optional): integer, default 20, max 100

**Headers**: `Authorization: Bearer <token>`

**Success Response** (200 OK):
```json
{
  "books": [
    {
      "id": 1,
      "title": "The Great Gatsby",
      "author": "F. Scott Fitzgerald",
      "isbn": "9780743273565",
      "publicationYear": 1925,
      "status": "AVAILABLE"
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

## GET /api/v1/books/:id

Get a single book by ID. Requires authentication.

**Headers**: `Authorization: Bearer <token>`

**Success Response** (200 OK):
```json
{
  "id": 1,
  "title": "The Great Gatsby",
  "author": "F. Scott Fitzgerald",
  "isbn": "9780743273565",
  "publicationYear": 1925,
  "status": "AVAILABLE",
  "createdAt": "2026-06-19T00:00:00.000Z",
  "updatedAt": "2026-06-19T00:00:00.000Z"
}
```

**Error Responses**:
- 404: `{ "error": "Book not found" }`

---

## POST /api/v1/books

Create a new book. Admin only.

**Headers**: `Authorization: Bearer <token>`

**Request Body**:
```json
{
  "title": "The Great Gatsby",
  "author": "F. Scott Fitzgerald",
  "isbn": "9780743273565",
  "publicationYear": 1925
}
```

**Validation Rules**:
- `title`: string, 1–255 chars, required
- `author`: string, 1–255 chars, required
- `isbn`: string, valid ISBN-10/ISBN-13 format, required, unique
- `publicationYear`: integer, 1000–current year, optional

**Success Response** (201 Created):
```json
{
  "id": 1,
  "title": "The Great Gatsby",
  "author": "F. Scott Fitzgerald",
  "isbn": "9780743273565",
  "publicationYear": 1925,
  "status": "AVAILABLE"
}
```

**Error Responses**:
- 400: `{ "error": "Validation failed", "details": [...] }`
- 403: `{ "error": "Admin privileges required" }`
- 409: `{ "error": "Book with this ISBN already exists" }`

---

## PUT /api/v1/books/:id

Update an existing book. Admin only.

**Headers**: `Authorization: Bearer <token>`

**Request Body**: (all fields optional)
```json
{
  "title": "The Great Gatsby (Updated)",
  "author": "F. Scott Fitzgerald",
  "isbn": "9780743273565",
  "publicationYear": 1925
}
```

**Success Response** (200 OK): Same as GET /books/:id

**Error Responses**:
- 400: `{ "error": "Validation failed", "details": [...] }`
- 403: `{ "error": "Admin privileges required" }`
- 404: `{ "error": "Book not found" }`

---

## DELETE /api/v1/books/:id

Delete a book. Admin only. Cannot delete books with active borrows.

**Headers**: `Authorization: Bearer <token>`

**Success Response** (200 OK):
```json
{
  "message": "Book deleted successfully"
}
```

**Error Responses**:
- 403: `{ "error": "Admin privileges required" }`
- 404: `{ "error": "Book not found" }`
- 409: `{ "error": "Cannot delete book with active borrows" }`
