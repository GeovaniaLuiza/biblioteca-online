# Data Model: Biblioteca Online

## Entity Relationship Summary

```
User 1───* BorrowRecord *───1 Book
```

## Entities

### User

| Field          | Type     | Constraints                      |
|----------------|----------|----------------------------------|
| id             | Int      | PK, auto-increment               |
| name           | String   | NOT NULL                         |
| email          | String   | NOT NULL, UNIQUE                 |
| passwordHash   | String   | NOT NULL (bcrypt hash)           |
| role           | Enum     | NOT NULL (USER or ADMIN)         |
| createdAt      | DateTime | NOT NULL, auto                   |
| updatedAt      | DateTime | NOT NULL, auto                   |

**Relations**: Has many BorrowRecords.

### Book

| Field          | Type     | Constraints                      |
|----------------|----------|----------------------------------|
| id             | Int      | PK, auto-increment               |
| title          | String   | NOT NULL                         |
| author         | String   | NOT NULL                         |
| isbn           | String   | NOT NULL, UNIQUE                 |
| publicationYear| Int      | nullable                         |
| status         | Enum     | NOT NULL (AVAILABLE or BORROWED) |
| createdAt      | DateTime | NOT NULL, auto                   |
| updatedAt      | DateTime | NOT NULL, auto                   |

**Relations**: Has many BorrowRecords.

### BorrowRecord

| Field          | Type     | Constraints                      |
|----------------|----------|----------------------------------|
| id             | Int      | PK, auto-increment               |
| userId         | Int      | FK → User.id, NOT NULL           |
| bookId         | Int      | FK → Book.id, NOT NULL           |
| borrowDate     | DateTime | NOT NULL, auto (on create)       |
| dueDate        | DateTime | NOT NULL                         |
| returnDate     | DateTime | nullable (set on return)         |
| status         | Enum     | NOT NULL (ACTIVE, RETURNED, OVERDUE) |
| processedBy    | Int      | FK → User.id, nullable (admin who processed return) |
| lateFee        | Boolean  | NOT NULL, default false          |
| createdAt      | DateTime | NOT NULL, auto                   |
| updatedAt      | DateTime | NOT NULL, auto                   |

**Relations**: Belongs to User, belongs to Book.

## Enums

- **Role**: `USER`, `ADMIN`
- **BookStatus**: `AVAILABLE`, `BORROWED`
- **BorrowStatus**: `ACTIVE`, `RETURNED`, `OVERDUE`

## Validation Rules

- **User email**: Must be valid email format, max 255 chars
- **User password**: Minimum 8 characters, maximum 128
- **User name**: Required, 1–100 characters
- **Book ISBN**: Valid ISBN-10 or ISBN-13 format
- **Book title/author**: Required, 1–255 characters
- **BorrowRecord dueDate**: Must be after borrowDate, default 14 days from borrow
- **Max borrows per user**: 5 active borrows at any time (application-level constraint)

## Indexes

- `User.email` — unique index
- `Book.isbn` — unique index
- `Book.title` — b-tree index for search
- `Book.author` — b-tree index for search
- `BorrowRecord.userId` — index for user lookups
- `BorrowRecord.bookId` — index for book lookups
- `BorrowRecord.status` — index for active borrow queries
