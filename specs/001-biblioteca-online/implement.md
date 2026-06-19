# Implementation Plan: Biblioteca Online

**Branch**: `feat/biblioteca-online` | **Date**: 2026-06-19

**Input**: Spec (`spec.md`), Plan (`plan.md`), Data Model (`data-model.md`), Tasks (`tasks.md`)

## Execution Order

### Phase 1: Project Setup (Tasks T001–T006)

**Goal**: Initialize monorepo with all tooling and dependencies.

**Steps**:
1. Create root directory structure: `backend/`, `frontend/`
2. Initialize `backend/` with `npm init`, install dependencies:
   - express, @types/express, prisma, @prisma/client, zod, jsonwebtoken, @types/jsonwebtoken, bcryptjs, @types/bcryptjs, cors, @types/cors
   - Dev: typescript, tsx, vitest, @types/node, eslint, prettier
3. Initialize `frontend/` with Vite React TypeScript template:
   - `npm create vite@latest frontend -- --template react-ts`
   - Install: react-router-dom, axios, tailwindcss, @tailwindcss/vite
   - Dev: vitest, @testing-library/react, @testing-library/jest-dom, jsdom, eslint, prettier
4. Configure TypeScript strict mode in both `tsconfig.json` files
5. Configure ESLint flat config and `.prettierrc`
6. Create `docker-compose.yml` with PostgreSQL service, API service, and Frontend service

**Validation**: Both projects compile without errors. `npm run dev` starts both servers.

---

### Phase 2: Foundation (Tasks T007–T014)

**Goal**: Database schema, API middleware, frontend shell.

**Backend**:
1. Write `backend/prisma/schema.prisma` with all 3 models, enums, indexes per data-model.md
2. Run `npx prisma migrate dev --name init`
3. Create `backend/src/server.ts`: Express app with CORS, JSON parsing, route mounting
4. Create `backend/src/middleware/error-handler.ts`: catch-all error handler returning `{ error, details }`
5. Create `backend/src/middleware/validate.ts`: Zod schema validation middleware factory
6. Create `backend/src/shared/types.ts`: interfaces for AuthPayload, PaginationParams, ApiResponse

**Frontend**:
7. Create `frontend/src/App.tsx` with React Router (BrowserRouter, Routes, Route)
8. Create `frontend/src/components/Layout.tsx` with header/main/footer structure
9. Create `frontend/src/services/api.ts`: Axios instance with `BASE_URL`, request interceptor for JWT, response interceptor for 401 redirect

**Validation**: Backend starts and responds to health check. Frontend renders layout with routing.

---

### Phase 3: User Story 1 — Auth (Tasks T015–T024)

**Goal**: Complete authentication flow.

**Backend**:
1. `backend/src/modules/auth/auth.schema.ts`: Zod schemas for register body, login body
2. `backend/src/modules/auth/auth.service.ts`:
   - `register(data)`: hash password with bcrypt, create user in DB, generate JWT, return user + token
   - `login(email, password)`: find user by email, compare password hash, generate JWT, return user + token
   - `getUserById(id)`: return user without password hash
3. `backend/src/modules/auth/auth.controller.ts`:
   - `register` handler: validate → service.register → 201
   - `login` handler: validate → service.login → 200
   - `getMe` handler: extract user from req → 200
4. `backend/src/middleware/auth.ts`:
   - `authenticate`: extract Bearer token, verify JWT, attach user to request
   - `requireRole(...roles)`: middleware factory checking user.role
5. Wire routes in `backend/src/app.ts`: `router.use('/auth', authRoutes)`

**Frontend**:
6. `frontend/src/pages/Login.tsx`: form with email/password fields, calls api, stores token, redirects
7. `frontend/src/pages/Register.tsx`: form with name/email/password fields, calls api, stores token, redirects
8. `frontend/src/hooks/useAuth.ts`: context provider with login/logout/register functions, token persistence in localStorage, auto-redirect on 401
9. `frontend/src/components/ProtectedRoute.tsx`: checks auth state, redirects to /login if unauthenticated
10. `frontend/src/components/Navbar.tsx`: shows Login/Register vs user name/Logout based on auth state

---

### Phase 4: User Story 2 — Catalog (Tasks T025–T032)

**Goal**: Full book CRUD with search.

**Backend**:
1. `backend/src/modules/books/books.schema.ts`: Zod schemas for create book, update book, query params
2. `backend/src/modules/books/books.service.ts`:
   - `list(query)`: paginated search by title/author with status filter
   - `getById(id)`: return book or throw NotFound
   - `create(data)`: check ISBN uniqueness, create book
   - `update(id, data)`: check existence, update fields
   - `delete(id)`: check no active borrows, delete book
3. `backend/src/modules/books/books.controller.ts`: CRUD handlers with admin guards on create/update/delete
4. Wire routes in `backend/src/app.ts`

**Frontend**:
5. `frontend/src/pages/Catalog.tsx`: book list with search input, pagination controls, each book links to detail
6. `frontend/src/pages/BookDetail.tsx`: full book info, availability badge, borrow button (if available)
7. `frontend/src/pages/AdminBookForm.tsx`: form for create/edit, admin-only route
8. `frontend/src/hooks/useBooks.ts`: fetch books, search, CRUD operations

---

### Phase 5: User Story 3 — Borrow (Tasks T033–T038)

**Goal**: Users can borrow books.

**Backend**:
1. `backend/src/modules/borrows/borrows.schema.ts`: Zod schema for borrow request body
2. `backend/src/modules/borrows/borrows.service.ts`:
   - `borrow(userId, bookId)`: check book available, check <5 active borrows, check no overdue, find or create borrow with 14-day due date, update book status to BORROWED
   - `getUserBorrows(userId, query)`: paginated list with status filter, include book info
3. `backend/src/modules/borrows/borrows.controller.ts`: borrow and list handlers
4. Wire routes in `backend/src/app.ts`

**Frontend**:
5. `frontend/src/pages/MyBorrows.tsx`: list of active borrows with book info, due dates, return button
6. Add borrow button on `Catalog.tsx` and `BookDetail.tsx`

---

### Phase 6: User Story 4 — Return (Tasks T039–T042)

**Goal**: Complete borrow/return cycle.

**Backend**:
1. Extend `borrows.service.ts`:
   - `returnBorrow(borrowId, userId, isAdmin)`: find borrow, verify ownership (or admin), set returnDate, calculate late fee (if past dueDate), update borrow status, update book status to AVAILABLE
2. Add return endpoint to `borrows.controller.ts`
3. Wire return route in `backend/src/app.ts`

**Frontend**:
4. Add return button and handler in `MyBorrows.tsx`
5. Add admin return view (optional: extend MyBorrows for admin to see all active borrows)

---

### Phase 7: User Story 5 — History (Tasks T043–T047)

**Goal**: Full history tracking.

**Backend**:
1. `backend/src/modules/history/history.service.ts`:
   - `getMyHistory(userId, query)`: query BorrowRecord by userId with book include, paginated
   - `getUserHistory(adminId, targetUserId, query)`: verify admin, query by targetUserId
   - `getAllHistory(adminId, query)`: verify admin, query all with user and book includes, filterable by userId/status
2. `backend/src/modules/history/history.controller.ts`: three handlers with appropriate guards
3. Wire routes in `backend/src/app.ts`

**Frontend**:
4. `frontend/src/pages/History.tsx`: user's own history with status filter, borrow/return dates
5. Add admin history dashboard (admin-only route showing all transactions)

---

### Phase 8: Polish (Tasks T048–T055)

**Goal**: Quality, testing, documentation.

**Tests**:
1. `backend/tests/integration/auth.test.ts`: register → login → getMe → invalid credentials
2. `backend/tests/integration/books.test.ts`: CRUD as admin → access denied as user
3. `backend/tests/integration/borrows.test.ts`: borrow → return → duplicate borrow → max limit
4. `backend/tests/integration/history.test.ts`: user history → admin all-history → access control

**Frontend**:
5. Add error handling (toast/alert on API errors) across all pages
6. Add loading spinners during API calls
7. Add empty state messages ("No books found", "No borrows yet", etc.)

**Database**:
8. `backend/prisma/seed.ts`: sample users (admin + regular), sample books, sample borrow records

**Documentation**:
9. `README.md`: project overview, setup steps, tech stack, API summary, docker-compose usage

---

## Verification Checklist

- [ ] `npm run dev` starts both backend and frontend
- [ ] User can register and login
- [ ] Admin can create/edit/delete books
- [ ] Regular user can browse and search books
- [ ] User can borrow available books
- [ ] System enforces max 5 concurrent borrows
- [ ] User can return borrowed books
- [ ] Late fees flagged for overdue returns
- [ ] User can view their borrowing history
- [ ] Admin can view all users' history
- [ ] Unauthorized access returns proper errors
- [ ] Integration tests pass
- [ ] Seed script populates sample data
