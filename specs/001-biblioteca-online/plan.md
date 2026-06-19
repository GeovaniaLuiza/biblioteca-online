# Implementation Plan: Biblioteca Online

**Branch**: `feat/biblioteca-online` | **Date**: 2026-06-19 | **Spec**: `specs/001-biblioteca-online/spec.md`

**Input**: Feature specification from `specs/001-biblioteca-online/spec.md`

## Summary

A full-stack web application for library management supporting user registration/authentication, book catalog management, borrowing/return workflows, and history tracking. The system uses a React frontend with a Node.js/Express REST API backend and PostgreSQL database.

## Technical Context

**Language/Version**: TypeScript 5.x (strict mode) for both frontend and backend

**Primary Dependencies**:
- Backend: Express.js, Prisma ORM, Zod, jsonwebtoken, bcryptjs
- Frontend: React 18, React Router v6, Axios, Tailwind CSS
- Dev: Vitest, tsx, pnpm

**Storage**: PostgreSQL 16 with Prisma ORM for schema management and migrations

**Testing**: Vitest for unit/integration tests (backend), Vitest + React Testing Library (frontend)

**Target Platform**: Web (modern browsers — Chrome, Firefox, Safari, Edge)

**Project Type**: Full-stack web application (monorepo with `backend/` and `frontend/` directories)

**Performance Goals**: API responses under 500ms p95 for all endpoints; catalog search returns in under 2 seconds

**Constraints**: Maximum 5 concurrent borrows per user; 14-day default borrowing period; JWT token expiry of 24 hours

**Scale/Scope**: Single library branch; up to 10k users; up to 50k books

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

- ✅ **Principle I (Web App Architecture)**: Three-tier architecture (React → Express API → PostgreSQL) satisfied.
- ✅ **Principle II (RESTful API)**: All endpoints follow REST conventions with JSON responses.
- ✅ **Principle III (Test-First)**: Tests will be written before implementation for each user story.
- ✅ **Principle IV (Security & Auth)**: JWT + bcrypt for auth; role-based access for admin operations.
- ✅ **Principle V (Data Integrity)**: Zod validation at API boundary; Prisma enforces DB constraints.

All gates pass. No violations to justify.

## Project Structure

### Documentation (this feature)

```text
specs/001-biblioteca-online/
├── plan.md              # This file
├── research.md          # Phase 0 output
├── data-model.md        # Phase 1 output
├── quickstart.md        # Phase 1 output
├── spec.md              # Feature specification
├── tasks.md             # Development tasks
├── issues.md            # GitHub issues mapping
├── implement.md         # Implementation plan
├── checklists/          # Quality checklists
└── contracts/           # API contracts
```

### Source Code (repository root)

```text
backend/
├── prisma/
│   └── schema.prisma
├── src/
│   ├── middleware/
│   │   ├── auth.ts
│   │   ├── error-handler.ts
│   │   └── validate.ts
│   ├── modules/
│   │   ├── auth/
│   │   │   ├── auth.controller.ts
│   │   │   ├── auth.service.ts
│   │   │   └── auth.schema.ts
│   │   ├── books/
│   │   │   ├── books.controller.ts
│   │   │   ├── books.service.ts
│   │   │   └── books.schema.ts
│   │   ├── borrows/
│   │   │   ├── borrows.controller.ts
│   │   │   ├── borrows.service.ts
│   │   │   └── borrows.schema.ts
│   │   └── history/
│   │       └── history.controller.ts
│   │       └── history.service.ts
│   ├── shared/
│   │   ├── types.ts
│   │   └── errors.ts
│   ├── app.ts
│   └── server.ts
├── tests/
│   ├── integration/
│   └── unit/
├── package.json
├── tsconfig.json
└── vitest.config.ts

frontend/
├── src/
│   ├── components/
│   │   ├── Layout.tsx
│   │   ├── ProtectedRoute.tsx
│   │   └── Navbar.tsx
│   ├── pages/
│   │   ├── Login.tsx
│   │   ├── Register.tsx
│   │   ├── Catalog.tsx
│   │   ├── BookDetail.tsx
│   │   ├── MyBorrows.tsx
│   │   ├── History.tsx
│   │   └── AdminBookForm.tsx
│   ├── services/
│   │   └── api.ts
│   ├── hooks/
│   │   ├── useAuth.ts
│   │   └── useBooks.ts
│   ├── App.tsx
│   └── main.tsx
├── tests/
├── package.json
├── tsconfig.json
├── vite.config.ts
└── tailwind.config.js
```

**Structure Decision**: Monorepo with separate `backend/` and `frontend/` directories at the repository root. This provides clear separation of concerns while keeping both parts in a single repository for simplified development and CI/CD.

## Complexity Tracking

No complexity violations — the monorepo structure with 2 projects (frontend + backend) is appropriate for the scale and matches the three-tier architecture principle.
