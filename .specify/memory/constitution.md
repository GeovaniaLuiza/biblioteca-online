<!--
  Sync Impact Report:
  - Version change: v0.0.0 → v1.0.0 (initial ratification)
  - All sections created new
  - Templates requiring updates: ✅ plan-template.md (updated), ✅ spec-template.md (updated), ✅ tasks-template.md (updated)
  - Follow-up TODOs: none
-->

# Biblioteca Online Constitution

## Core Principles

### I. Web Application Architecture

Every feature is delivered as a web application component with clear separation of concerns. The system follows a three-tier architecture: presentation (frontend), business logic (API/services), and data (database). Each layer MUST be independently testable and deployable. Clear API contracts required between frontend and backend — no direct database access from the presentation layer.

### II. RESTful API Design

All backend functionality MUST be exposed via RESTful HTTP endpoints following standard conventions (resource-based URLs, proper HTTP methods, consistent response formats). JSON in/out protocol: request bodies → JSON responses, errors → structured JSON error objects with appropriate HTTP status codes. API versioning via URL prefix (e.g., `/api/v1/`).

### III. Test-First Development (NON-NEGOTIABLE)

TDD is mandatory for all feature work. Tests MUST be written and approved by the user BEFORE implementation begins. Red-Green-Refactor cycle strictly enforced:
- **Red**: Write a failing test that defines the expected behavior
- **Green**: Implement the minimum code to make the test pass
- **Refactor**: Clean up code while keeping tests green
- Tests MUST fail before implementation to prove they can detect defects.

### IV. Security & Authentication

Authentication and authorization MUST be implemented before any user-facing features. The system SHALL support two roles: `admin` and `user`. All mutations (create, update, delete) MUST be authorized. Password storage MUST use bcrypt or Argon2 hashing. Session management via JWT tokens with configurable expiration. Admin operations require elevated privileges verified on every request.

### V. Data Integrity & Validation

All data entering the system MUST be validated at the API boundary before reaching the business layer or database. Validation rules MUST be defined declaratively (not scattered in business logic). Database constraints (foreign keys, unique indexes, not-null) MUST enforce referential integrity at the storage level. Soft deletes SHOULD be preferred over hard deletes for auditability.

## Technology Stack

### Backend
- **Runtime**: Node.js 20 LTS+
- **Framework**: Express.js or Fastify for REST API
- **Language**: TypeScript (strict mode)
- **ORM**: Prisma or Drizzle for database access
- **Database**: PostgreSQL 16
- **Auth**: JWT (jsonwebtoken) + bcrypt
- **Validation**: Zod for request/response schemas
- **Testing**: Vitest for unit/integration tests

### Frontend
- **Framework**: React 18+ with TypeScript
- **Build Tool**: Vite
- **Routing**: React Router v6
- **HTTP Client**: fetch API or Axios
- **Styling**: CSS Modules or Tailwind CSS
- **Testing**: Vitest + React Testing Library

### Infrastructure
- **Containerization**: Docker + docker-compose for local dev
- **CI/CD**: GitHub Actions
- **Version Control**: Git with conventional commits
- **Package Manager**: npm or pnpm

## Development Workflow

### Branch Strategy

- `main` — production-ready code, protected, requires PR review
- `develop` — integration branch for feature work
- `feat/<feature-name>` — individual feature branches
- `fix/<bug-description>` — bug fix branches

### Commit Convention

All commits MUST follow [Conventional Commits](https://www.conventionalcommits.org/):
- `feat:` — new feature
- `fix:` — bug fix
- `docs:` — documentation changes
- `test:` — adding or modifying tests
- `refactor:` — code restructuring
- `chore:` — maintenance tasks

### Review Process

- All PRs require at least one approving review
- Tests MUST pass before merge
- No failing tests allowed on `main` or `develop`
- Constitution compliance MUST be verified during review

## Governance

This Constitution supersedes all other project practices. Amendments require:
1. Documentation of the proposed change
2. Approval from the project maintainer
3. A migration plan for existing code if backward-incompatible
4. Version bump according to semver rules applied to governance

All PRs and reviews MUST verify compliance with this Constitution. Complexity MUST be justified — any deviation from these principles requires explicit documented rationale.

**Version**: 1.0.0 | **Ratified**: 2026-06-19 | **Last Amended**: 2026-06-19
