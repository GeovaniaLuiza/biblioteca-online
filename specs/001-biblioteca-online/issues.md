# GitHub Issues: Biblioteca Online

**Mapping**: Tasks → GitHub Issues organized by milestones

**Repository Owner/Name**: `biblioteca-online` (to be created on GitHub)

---

## Milestone 1: Foundation

### Issue 1: Project Setup & Infrastructure

**Labels**: `setup`, `infrastructure`, `p0`

**Description**:
Initialize the monorepo with backend (Node.js/TypeScript/Express) and frontend (React/Vite/TypeScript) projects. Configure tooling (ESLint, Prettier, TypeScript strict mode) and Docker Compose for local development.

**Tasks**: T001, T002, T003, T004, T005, T006

**Acceptance Criteria**:
- [ ] `backend/` and `frontend/` directories exist with proper project structure
- [ ] Both projects compile with TypeScript strict mode
- [ ] Docker Compose runs PostgreSQL, API, and Frontend locally
- [ ] ESLint and Prettier are configured

---

### Issue 2: Database Schema & API Foundation

**Labels**: `database`, `backend`, `p0`

**Description**:
Define the Prisma schema with User, Book, and BorrowRecord models. Create the Express server with CORS, error handling middleware, Zod validation middleware, and global type definitions.

**Tasks**: T007, T008, T009, T010, T011, T012

**Acceptance Criteria**:
- [ ] Prisma schema matches data-model.md with all entities, enums, indexes, and relations
- [ ] Migration runs successfully against PostgreSQL
- [ ] Express server starts and handles requests
- [ ] Error handler returns consistent JSON error responses
- [ ] Zod validation rejects invalid request bodies

---

### Issue 3: Frontend Shell & API Client

**Labels**: `frontend`, `p0`

**Description**:
Set up the React application shell with routing (React Router), layout component, and the Axios API client with JWT interceptor.

**Tasks**: T013, T014

**Acceptance Criteria**:
- [ ] React app renders with navigation layout
- [ ] React Router is configured with placeholder routes
- [ ] API client makes requests with proper base URL
- [ ] JWT token is attached to Authorization header when present

---

## Milestone 2: MVP — Auth & Catalog

### Issue 4: User Authentication (Backend)

**Labels**: `backend`, `auth`, `p1`, `user-story-1`

**Description**:
Implement complete backend authentication: registration with bcrypt password hashing, login with JWT token generation, and JWT verification middleware with role-based access.

**Tasks**: T015, T016, T017, T018, T019

**Acceptance Criteria**:
- [ ] POST /api/v1/auth/register creates user and returns JWT
- [ ] POST /api/v1/auth/login validates credentials and returns JWT
- [ ] GET /api/v1/auth/me returns authenticated user profile
- [ ] Protected routes reject requests without valid JWT
- [ ] Admin-only routes reject non-admin users with 403

---

### Issue 5: User Authentication (Frontend)

**Labels**: `frontend`, `auth`, `p1`, `user-story-1`

**Description**:
Implement the login, registration, and protected route UI. Create authentication state management hook and navbar with auth-aware navigation.

**Tasks**: T020, T021, T022, T023, T024

**Acceptance Criteria**:
- [ ] Login page accepts email/password and redirects to dashboard
- [ ] Register page accepts name/email/password and creates account
- [ ] Unauthenticated users are redirected to login for protected routes
- [ ] Navbar shows login/register or user info/logout based on auth state
- [ ] Auth state persists across page reloads (JWT in localStorage)

---

### Issue 6: Book Catalog (Backend)

**Labels**: `backend`, `catalog`, `p1`, `user-story-2`

**Description**:
Implement CRUD endpoints for book catalog with search filtering, pagination, and admin-only guards for create/update/delete operations.

**Tasks**: T025, T026, T027, T028

**Acceptance Criteria**:
- [ ] GET /api/v1/books lists books with search and pagination
- [ ] GET /api/v1/books/:id returns single book details
- [ ] POST /api/v1/books creates book (admin only)
- [ ] PUT /api/v1/books/:id updates book (admin only)
- [ ] DELETE /api/v1/books/:id deletes book (admin only, blocks if active borrows)
- [ ] Regular users get 403 on admin operations

---

### Issue 7: Book Catalog (Frontend)

**Labels**: `frontend`, `catalog`, `p1`, `user-story-2`

**Description**:
Build the book catalog UI with search, pagination, book detail view, and admin book creation/editing forms.

**Tasks**: T029, T030, T031, T032

**Acceptance Criteria**:
- [ ] Catalog page displays books in a list/grid with search bar
- [ ] Search filters books by title/author as user types
- [ ] Book detail page shows full book info and availability status
- [ ] Admin users see Add/Edit/Delete buttons
- [ ] AdminBookForm creates and updates books
- [ ] Form validation matches API schemas

---

## Milestone 3: Borrow & Return

### Issue 8: Borrowing System (Backend)

**Labels**: `backend`, `borrowing`, `p2`, `user-story-3`

**Description**:
Implement borrowing logic: borrow book with availability check, concurrent borrow limit, and overdue prevention. Return endpoint with late fee detection.

**Tasks**: T033, T034, T035, T036, T039, T040

**Acceptance Criteria**:
- [ ] POST /api/v1/borrows creates borrow with 14-day due date
- [ ] Borrowing an unavailable book returns 400 error
- [ ] Exceeding 5 active borrows returns 400 error
- [ ] User with overdue books cannot borrow new ones
- [ ] POST /api/v1/borrows/:id/return marks book as returned
- [ ] Returns after due date flag lateFee as true
- [ ] Book status toggles between AVAILABLE/BORROWED

---

### Issue 9: Borrowing System (Frontend)

**Labels**: `frontend`, `borrowing`, `p2`, `user-story-3`

**Description**:
Build the borrowing UI: borrow button on book pages, my borrows page showing active borrows with return functionality.

**Tasks**: T037, T038, T041, T042

**Acceptance Criteria**:
- [ ] "Borrow" button appears on available books for authenticated users
- [ ] Clicking borrow shows confirmation and updates book status
- [ ] MyBorrows page shows user's active borrows with due dates
- [ ] "Return" button on active borrows processes return
- [ ] Admin can see and process returns for any user
- [ ] UI shows overdue status with visual indicator

---

## Milestone 4: History & Polish

### Issue 10: History Tracking (Backend)

**Labels**: `backend`, `history`, `p3`, `user-story-5`

**Description**:
Implement history endpoints: user's own history, admin view of any user's history, and global all-users history with filtering and pagination.

**Tasks**: T043, T044, T045

**Acceptance Criteria**:
- [ ] GET /api/v1/history/me returns authenticated user's history
- [ ] GET /api/v1/history/users/:userId returns specific user's history (admin only)
- [ ] GET /api/v1/history/all returns all transactions (admin only)
- [ ] Results are paginated and filterable by status
- [ ] Regular users cannot access other users' history

---

### Issue 11: History Tracking (Frontend)

**Labels**: `frontend`, `history`, `p3`, `user-story-5`

**Description**:
Build the history UI: user history page with filtering, and admin dashboard for viewing all transactions.

**Tasks**: T046, T047

**Acceptance Criteria**:
- [ ] History page shows user's complete borrowing history
- [ ] Results are filterable by borrow status
- [ ] Admin can view any user's history via admin dashboard
- [ ] Admin dashboard shows all transactions with user info

---

### Issue 12: Testing & Polish

**Labels**: `testing`, `polish`, `p3`

**Description**:
Write integration tests for all API endpoints, add frontend error handling, loading states, seed data script, and documentation.

**Tasks**: T048, T049, T050, T051, T052, T053, T054, T055

**Acceptance Criteria**:
- [ ] Integration tests cover auth, books, borrows, and history flows
- [ ] All API errors display user-friendly messages on frontend
- [ ] Loading spinners/skeletons show during API calls
- [ ] Empty states display when no data available
- [ ] Seed script populates database with sample data
- [ ] README explains setup, architecture, and API endpoints

---

## Issue Labels Reference

| Label         | Description                              |
|---------------|------------------------------------------|
| `p0`          | Foundation — blocks everything           |
| `p1`          | MVP — essential for v1                   |
| `p2`          | Core feature — needed for full workflow  |
| `p3`          | Enhancement — valuable but not blocking  |
| `backend`     | Backend/API work                         |
| `frontend`    | Frontend/UI work                         |
| `auth`        | Authentication & authorization           |
| `catalog`     | Book catalog management                  |
| `borrowing`   | Borrow/return system                     |
| `history`     | History tracking                         |
| `database`    | Database schema/migrations               |
| `testing`     | Test coverage                            |
| `setup`       | Project initialization                   |
| `infrastructure` | Docker, CI/CD, config               |
| `polish`      | UX improvements, error handling, docs    |
