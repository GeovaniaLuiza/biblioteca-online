# Quickstart Guide: Biblioteca Online

## Prerequisites

- Node.js 20 LTS+
- Docker Desktop (for PostgreSQL)
- npm or pnpm

## Setup

```bash
# 1. Install backend dependencies
cd backend
npm install

# 2. Install frontend dependencies
cd ../frontend
npm install

# 3. Start PostgreSQL via Docker
cd ..
docker compose up -d postgres

# 4. Run database migrations
cd backend
npx prisma migrate dev

# 5. Seed sample data
npx prisma db seed

# 6. Start backend (http://localhost:3001)
npm run dev

# 7. In another terminal, start frontend (http://localhost:5173)
cd frontend
npm run dev
```

## Validation Scenarios

### Scenario 1: User Registration & Login

```bash
# Register a new user
curl -X POST http://localhost:3001/api/v1/auth/register \
  -H "Content-Type: application/json" \
  -d '{"name":"Jane Doe","email":"jane@example.com","password":"password123"}'

# Expected: 201 with user object and JWT token

# Login
curl -X POST http://localhost:3001/api/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"jane@example.com","password":"password123"}'

# Expected: 200 with user object and JWT token
```

### Scenario 2: Book Catalog CRUD (Admin)

```bash
# Register as admin (or use seeded admin)
# Note: Seeded admin credentials: admin@biblioteca.com / admin123

# Login as admin → save TOKEN
TOKEN="<admin-jwt-token>"

# Create a book
curl -X POST http://localhost:3001/api/v1/books \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $TOKEN" \
  -d '{"title":"1984","author":"George Orwell","isbn":"9780451524935","publicationYear":1949}'

# Expected: 201 with book object

# Search books
curl http://localhost:3001/api/v1/books?search=1984 \
  -H "Authorization: Bearer $TOKEN"

# Expected: 200 with book list
```

### Scenario 3: Borrow & Return Flow

```bash
# Login as regular user → save TOKEN
TOKEN="<user-jwt-token>"

# View available books
curl http://localhost:3001/api/v1/books?status=AVAILABLE \
  -H "Authorization: Bearer $TOKEN"

# Borrow a book (replace BOOK_ID with actual ID)
curl -X POST http://localhost:3001/api/v1/borrows \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $TOKEN" \
  -d '{"bookId": 1}'

# Expected: 201 with borrow record

# View active borrows
curl http://localhost:3001/api/v1/borrows?status=ACTIVE \
  -H "Authorization: Bearer $TOKEN"

# Return the book (replace BORROW_ID with actual ID)
curl -X POST http://localhost:3001/api/v1/borrows/1/return \
  -H "Authorization: Bearer $TOKEN"

# Expected: 200 with returned borrow record
```

### Scenario 4: View History

```bash
# View own history
curl http://localhost:3001/api/v1/history/me \
  -H "Authorization: Bearer $TOKEN"

# Expected: 200 with paginated history list

# Admin: view all history (use admin token)
ADMIN_TOKEN="<admin-jwt-token>"
curl http://localhost:3001/api/v1/history/all \
  -H "Authorization: Bearer $ADMIN_TOKEN"

# Expected: 200 with all transactions
```

## Test Suite

```bash
# Run all backend tests
cd backend
npx vitest run

# Run all frontend tests
cd ../frontend
npx vitest run
```
