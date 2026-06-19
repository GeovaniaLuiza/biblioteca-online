# Feature Specification: Biblioteca Online — Library Management System

**Feature Branch**: `feat/biblioteca-online`

**Created**: 2026-06-19

**Status**: Draft

**Input**: User description: "Web application for library management with users (admin and regular users), book catalog, borrowing system, return system, and history tracking."

## User Scenarios & Testing

### User Story 1 — User Registration & Authentication (Priority: P1)

As a visitor, I want to create an account and log into the system so that I can access library services.

**Why this priority**: Authentication is a prerequisite for all other features — no borrowing, returning, or history tracking can work without identifying the user.

**Independent Test**: Can be fully tested by registering a new user, logging in with valid credentials, and verifying that invalid credentials are rejected. Delivers a working auth system that other features build upon.

**Acceptance Scenarios**:
1. **Given** a visitor with valid registration data, **When** they submit the registration form, **Then** an account is created and they receive a confirmation.
2. **Given** a registered user, **When** they log in with correct email and password, **Then** they receive an access token and are redirected to the dashboard.
3. **Given** a registered user, **When** they log in with incorrect password, **Then** they see an error message and are not authenticated.
4. **Given** an unauthenticated user, **When** they try to access a protected route (e.g., borrowing), **Then** they are redirected to the login page.

---

### User Story 2 — Book Catalog Management (Priority: P1)

As an admin, I want to manage the book catalog (add, edit, list, search books) so that the library collection is available for borrowing.

**Why this priority**: The book catalog is the core data entity — without books in the system, borrowing and returning have nothing to operate on.

**Independent Test**: Can be fully tested by an admin adding a book, searching for it, editing details, and verifying the changes persist. Delivers a functional catalog that admins can populate.

**Acceptance Scenarios**:
1. **Given** an authenticated admin, **When** they add a new book with title, author, ISBN, and publication year, **Then** the book appears in the catalog.
2. **Given** an authenticated admin, **When** they edit an existing book's details, **Then** the changes are saved and visible.
3. **Given** any authenticated user, **When** they search books by title or author, **Then** matching results are displayed.
4. **Given** a regular user (non-admin), **When** they try to add or edit a book, **Then** the operation is denied with a 403 Forbidden error.

---

### User Story 3 — Borrowing System (Priority: P2)

As a regular user, I want to borrow books from the catalog so that I can take them home to read.

**Why this priority**: Borrowing is the core lending functionality — it depends on both users (US1) and books (US2) existing in the system.

**Independent Test**: Can be fully tested by selecting an available book, borrowing it, and verifying the book's status changes to "borrowed" and a borrowing record is created.

**Acceptance Scenarios**:
1. **Given** an authenticated user with no overdue books, **When** they borrow an available book, **Then** the book status changes to "borrowed" and a borrowing record is created with a due date.
2. **Given** an authenticated user, **When** they try to borrow a book that is already borrowed by another user, **Then** they see a "book unavailable" message.
3. **Given** a user with overdue books, **When** they try to borrow another book, **Then** the system blocks the borrow and shows an overdue notice.
4. **Given** an unauthenticated visitor, **When** they try to access the borrow endpoint, **Then** they receive a 401 Unauthorized response.

---

### User Story 4 — Return System (Priority: P2)

As a regular user and as an admin, I want to return borrowed books so that the books become available for others.

**Why this priority**: Return completes the lending cycle — without it, books remain indefinitely borrowed and the catalog becomes unusable.

**Independent Test**: Can be fully tested by borrowing a book (US3), returning it, and verifying the book status changes back to "available" and the return date is recorded.

**Acceptance Scenarios**:
1. **Given** a user who has borrowed a book, **When** they return it before the due date, **Then** the book status changes to "available" and the return is timestamped.
2. **Given** a user returning a book after the due date, **When** the return is processed, **Then** the late return is accepted and a late fee flag is recorded.
3. **Given** an admin, **When** they process a return on behalf of a user, **Then** the system records the return with the admin's ID as the processor.

---

### User Story 5 — History Tracking (Priority: P3)

As a user and as an admin, I want to view borrowing history so that I can track past lending activity.

**Why this priority**: History is valuable but not critical — it depends on borrowing (US3) and returns (US4) already generating data.

**Independent Test**: Can be fully tested by performing borrow/return operations (US3, US4) and then querying the history endpoint to verify all transactions are recorded.

**Acceptance Scenarios**:
1. **Given** a regular user with past borrows and returns, **When** they view their history, **Then** they see their own borrowing record with book titles, borrow dates, return dates, and status.
2. **Given** an admin, **When** they view the history for any user, **Then** they can see that user's complete borrowing record.
3. **Given** an admin, **When** they view the global history dashboard, **Then** they see all borrowing transactions across all users.
4. **Given** a regular user, **When** they try to view another user's history, **Then** the system denies access with a 403 Forbidden error.

---

### Edge Cases

- What happens when a user tries to borrow more books than the configured limit?
- How does the system handle database connection failures during borrow/return operations?
- What happens when an admin deletes a book that has active borrows?
- How does the system handle concurrent borrow requests for the last available copy of a book?

## Requirements

### Functional Requirements

- **FR-001**: System MUST allow visitors to register with name, email, and password.
- **FR-002**: System MUST authenticate users via email and password, returning a JWT token.
- **FR-003**: System MUST support two roles: `admin` and `user`.
- **FR-004**: Admin users MUST be able to create, read, update, and delete books in the catalog.
- **FR-005**: Regular users MUST be able to view and search the book catalog.
- **FR-006**: Regular users MUST be able to view book details including availability status.
- **FR-007**: Users MUST be able to borrow available books.
- **FR-008**: System MUST prevent borrowing of books that are already borrowed.
- **FR-009**: System MUST enforce a maximum number of concurrent borrows per user.
- **FR-010**: Users MUST be able to return borrowed books.
- **FR-011**: System MUST record borrow date, due date, and return date for each transaction.
- **FR-012**: System MUST track the complete borrowing history per user.
- **FR-013**: Admin users MUST be able to view any user's borrowing history.
- **FR-014**: Admin users MUST be able to view the global borrowing history.
- **FR-015**: System MUST prevent unauthorized access to protected endpoints.
- **FR-016**: System MUST validate all input data before processing.
- **FR-017**: System MUST return appropriate HTTP status codes and error messages for all operations.

### Key Entities

- **User**: Represents a library member or administrator. Attributes include name, email, hashed password, role (admin/user), and account status.
- **Book**: Represents a physical book in the library catalog. Attributes include title, author, ISBN, publication year, and availability status.
- **BorrowRecord**: Represents a lending transaction linking a user to a book. Attributes include borrow date, due date, return date, status (active/returned/overdue), and late fee flag.

## Success Criteria

### Measurable Outcomes

- **SC-001**: A new user can complete registration in under 2 minutes.
- **SC-002**: An admin can add a new book to the catalog in under 1 minute.
- **SC-003**: Users can search the catalog and see results in under 3 seconds.
- **SC-004**: The borrow process (select book → confirm → receive confirmation) takes under 30 seconds.
- **SC-005**: A user can view their complete borrowing history in under 2 seconds.
- **SC-006**: The system correctly enforces authorization — unauthorized access attempts return errors 100% of the time.

## Assumptions

- Users have stable internet connectivity.
- The system will be deployed as a web application (not a mobile app) for v1.
- Email verification for registration is out of scope for v1.
- The library has physical books (one copy per catalog entry) — no multi-copy inventory tracking.
- The default borrowing period is 14 days.
- The maximum concurrent borrows per user is 5.
- Late fees are tracked but fee calculation/payment is out of scope for v1.
