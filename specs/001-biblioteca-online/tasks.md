---
description: "Task list for Biblioteca Online feature implementation"
---

# Tasks: Biblioteca Online

**Input**: Design documents from `specs/001-biblioteca-online/`

**Prerequisites**: plan.md (required), spec.md (required for user stories), data-model.md, contracts/

**Organization**: Tasks are grouped by user story to enable independent implementation and testing of each story.

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: Project initialization and basic structure

- [ ] T001 Create repository root structure with `backend/` and `frontend/` directories
- [ ] T002 [P] Initialize backend Node.js/TypeScript project with dependencies (express, prisma, zod, jsonwebtoken, bcryptjs, vitest)
- [ ] T003 [P] Initialize frontend React/TypeScript project with Vite (react-router-dom, axios, tailwindcss, vitest, @testing-library/react)
- [ ] T004 Configure TypeScript strict mode in both `backend/tsconfig.json` and `frontend/tsconfig.json`
- [ ] T005 Configure ESLint and Prettier for both projects
- [ ] T006 Create Docker Compose file for PostgreSQL + API + Frontend local development

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Core infrastructure that MUST be complete before ANY user story can be implemented

**⚠️ CRITICAL**: No user story work can begin until this phase is complete

- [ ] T007 Define Prisma schema (`backend/prisma/schema.prisma`) with User, Book, BorrowRecord models, enums, indexes, and relations per data-model.md
- [ ] T008 Create and run initial Prisma migration
- [ ] T009 Implement Express server setup in `backend/src/server.ts` with CORS, JSON body parsing, and route mounting
- [ ] T010 Implement error handling middleware in `backend/src/middleware/error-handler.ts`
- [ ] T011 Implement Zod validation middleware in `backend/src/middleware/validate.ts`
- [ ] T012 Implement global types in `backend/src/shared/types.ts` (AuthPayload, PaginationParams, etc.)
- [ ] T013 Setup React app shell in `frontend/src/App.tsx` with React Router and basic layout component in `frontend/src/components/Layout.tsx`
- [ ] T014 [P] Create API client service in `frontend/src/services/api.ts` with Axios instance and interceptor for JWT

**Checkpoint**: Foundation ready — user story implementation can now begin in parallel

---

## Phase 3: User Story 1 — User Registration & Authentication (Priority: P1) 🎯 MVP

**Goal**: Users can register and log into the system.

**Independent Test**: Register a new user via POST /api/v1/auth/register, login via POST /api/v1/auth/login, and verify the returned JWT grants access to GET /api/v1/auth/me.

### Implementation for User Story 1

- [ ] T015 [US1] Implement auth schemas (register/login request validation) in `backend/src/modules/auth/auth.schema.ts`
- [ ] T016 [US1] Implement auth service in `backend/src/modules/auth/auth.service.ts` (register with bcrypt hashing, login with password verification, JWT generation)
- [ ] T017 [US1] Implement auth controller in `backend/src/modules/auth/auth.controller.ts` (register, login, getMe endpoints)
- [ ] T018 [US1] Implement JWT authentication middleware in `backend/src/middleware/auth.ts` (token verification, role checking)
- [ ] T019 [US1] Wire auth routes into Express app in `backend/src/app.ts`
- [ ] T020 [P] [US1] Create Login page in `frontend/src/pages/Login.tsx`
- [ ] T021 [P] [US1] Create Register page in `frontend/src/pages/Register.tsx`
- [ ] T022 [US1] Create useAuth hook in `frontend/src/hooks/useAuth.ts` for login/register/logout state management
- [ ] T023 [US1] Implement ProtectedRoute component in `frontend/src/components/ProtectedRoute.tsx`
- [ ] T024 [US1] Implement Navbar component in `frontend/src/components/Navbar.tsx` with auth-aware navigation

**Checkpoint**: At this point, User Story 1 should be fully functional: users can register, log in, and see a basic authenticated UI.

---

## Phase 4: User Story 2 — Book Catalog Management (Priority: P1)

**Goal**: Admin can manage the book catalog; users can browse and search.

**Independent Test**: Admin adds a book via POST /api/v1/books, searches it via GET /api/v1/books?search=..., edits it via PUT /api/v1/books/:id, and deletes it via DELETE /api/v1/books/:id. Regular user can only view.

### Implementation for User Story 2

- [ ] T025 [P] [US2] Implement book schemas in `backend/src/modules/books/books.schema.ts` (create, update, query validation)
- [ ] T026 [US2] Implement books service in `backend/src/modules/books/books.service.ts` (CRUD operations with search filtering and pagination)
- [ ] T027 [US2] Implement books controller in `backend/src/modules/books/books.controller.ts` (CRUD endpoints with admin guards)
- [ ] T028 [US2] Wire book routes into Express app in `backend/src/app.ts`
- [ ] T029 [P] [US2] Create Catalog page in `frontend/src/pages/Catalog.tsx` with search bar and book list
- [ ] T030 [P] [US2] Create BookDetail page in `frontend/src/pages/BookDetail.tsx`
- [ ] T031 [P] [US2] Create AdminBookForm page in `frontend/src/pages/AdminBookForm.tsx` (create/edit book form)
- [ ] T032 [US2] Create useBooks hook in `frontend/src/hooks/useBooks.ts`

**Checkpoint**: At this point, User Stories 1 AND 2 should both work independently. Admin can manage catalog; all users can browse.

---

## Phase 5: User Story 3 — Borrowing System (Priority: P2)

**Goal**: Users can borrow available books.

**Independent Test**: User borrows an available book via POST /api/v1/borrows, verifies book status changes to BORROWED, and cannot borrow the same book again.

### Implementation for User Story 3

- [ ] T033 [P] [US3] Implement borrow schemas in `backend/src/modules/borrows/borrows.schema.ts` (borrow request validation)
- [ ] T034 [US3] Implement borrows service in `backend/src/modules/borrows/borrows.service.ts` (borrow logic with availability check, max limit check, overdue check)
- [ ] T035 [US3] Implement borrows controller in `backend/src/modules/borrows/borrows.controller.ts`
- [ ] T036 [US3] Wire borrow routes into Express app in `backend/src/app.ts`
- [ ] T037 [US3] Create MyBorrows page in `frontend/src/pages/MyBorrows.tsx` showing active borrows with borrow/return actions
- [ ] T038 [US3] Add "Borrow" button on BookDetail page and Catalog page

**Checkpoint**: At this point, User Stories 1–3 should all work. Users can browse the catalog, borrow books, and see their active borrows.

---

## Phase 6: User Story 4 — Return System (Priority: P2)

**Goal**: Users can return borrowed books; admins can process returns on behalf of users.

**Independent Test**: Borrow a book (US3), return it via POST /api/v1/borrows/:id/return, and verify book status changes back to AVAILABLE and borrow status changes to RETURNED.

### Implementation for User Story 4

- [ ] T039 [US4] Implement return logic in `backend/src/modules/borrows/borrows.service.ts` (return with late fee detection)
- [ ] T040 [US4] Add return endpoint in `backend/src/modules/borrows/borrows.controller.ts` (user can return own; admin can return any)
- [ ] T041 [US4] Add "Return" button and return flow in `frontend/src/pages/MyBorrows.tsx`
- [ ] T042 [US4] Add admin return interface in a new admin borrows view or extend MyBorrows for admin

**Checkpoint**: At this point, User Stories 1–4 should all work. Complete borrow/return cycle is functional.

---

## Phase 7: User Story 5 — History Tracking (Priority: P3)

**Goal**: Users can view their own borrowing history; admins can view all history.

**Independent Test**: Perform borrow/return operations, then query the history endpoints to verify all transactions are recorded and accessible with correct permissions.

### Implementation for User Story 5

- [ ] T043 [US5] Implement history service in `backend/src/modules/history/history.service.ts` (query by user, admin access to all)
- [ ] T044 [US5] Implement history controller in `backend/src/modules/history/history.controller.ts` (me, user-specific, all)
- [ ] T045 [US5] Wire history routes into Express app in `backend/src/app.ts`
- [ ] T046 [US5] Create History page in `frontend/src/pages/History.tsx` with filtering by status
- [ ] T047 [US5] Add admin history dashboard view showing all users' transactions

**Checkpoint**: All user stories should now be independently functional. Full system is complete.

---

## Phase 8: Polish & Cross-Cutting Concerns

**Purpose**: Improvements that affect multiple user stories

- [ ] T048 [P] Write integration tests for auth flow in `backend/tests/integration/auth.test.ts`
- [ ] T049 [P] Write integration tests for books CRUD in `backend/tests/integration/books.test.ts`
- [ ] T050 [P] Write integration tests for borrow/return flow in `backend/tests/integration/borrows.test.ts`
- [ ] T051 [P] Write integration tests for history endpoints in `backend/tests/integration/history.test.ts`
- [ ] T052 Add frontend error handling for API errors across all pages
- [ ] T053 Add loading states and empty state messages to all pages
- [ ] T054 Seed database script with sample data for development/demo in `backend/prisma/seed.ts`
- [ ] T055 Create README.md with setup instructions and API documentation

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies — can start immediately
- **Foundational (Phase 2)**: Depends on Setup completion — BLOCKS all user stories
- **User Stories (Phase 3–7)**: All depend on Foundational phase completion
  - US1 (P1) must be completed before US3–5 (since they require auth)
  - US2 (P1) must be completed before US3–5 (since they require books)
  - US3 (P2) must be completed before US4 (P2) (return depends on borrow)
  - US5 (P3) depends on US1, US2, US3, US4 (history needs borrow/return data)
- **Polish (Phase 8)**: Depends on all user stories being complete

### Parallel Opportunities

- All Setup tasks marked [P] can run in parallel
- T002 and T003 (frontend/backend init) can run in parallel
- Within each user story, UI tasks marked [P] can run in parallel with backend tasks

### Implementation Strategy

1. Complete Phase 1: Setup
2. Complete Phase 2: Foundational (CRITICAL — blocks all stories)
3. Complete Phase 3: User Story 1 (MVP: authentication)
4. Complete Phase 4: User Story 2 (MVP: catalog)
5. **STOP and VALIDATE**: Auth + Catalog works end-to-end
6. Complete Phase 5: User Story 3 (borrowing)
7. Complete Phase 6: User Story 4 (returns)
8. Complete Phase 7: User Story 5 (history)
9. Complete Phase 8: Polish and tests

### Incremental Delivery

1. Setup + Foundational → Foundation ready
2. Add US1 (auth) → MVP: login/register works
3. Add US2 (catalog) → MVP: admin can manage books, users can browse
4. Add US3 (borrow) → Users can borrow books
5. Add US4 (return) → Full lend/return cycle
6. Add US5 (history) → Complete library management system
