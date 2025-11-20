# Project Memory

## What We're Building
[One paragraph elevator pitch - Replace this with your project description]

Example: A SaaS platform for freelancers to manage their invoices, track time, and get paid faster. Stripe integration for payments, automated invoice generation, and client portal.

## Tech Stack
- **Frontend**: React 18 + TypeScript + Vite + Tailwind + shadcn/ui
- **Backend**: Node.js 20 + Express + TypeScript + Prisma ORM
- **Database**: PostgreSQL 15
- **Auth**: JWT with httpOnly cookies
- **Payments**: Stripe
- **Hosting**: Vercel (frontend) + Railway (backend)
- **Testing**: Vitest (unit) + Playwright (E2E)

## Directory Structure

```
src/
├── client/
│   ├── components/
│   │   ├── ui/              # shadcn/ui base components
│   │   └── features/        # Feature-specific components
│   ├── pages/               # Route pages (React Router)
│   ├── hooks/               # Custom hooks (useAuth, useSubscription)
│   ├── lib/                 # Utilities, API client, helpers
│   └── types/               # TypeScript types
│
├── server/
│   ├── api/                 # Route handlers (thin controllers)
│   ├── services/            # Business logic (fat services)
│   ├── models/              # Prisma models
│   ├── middleware/          # Express middleware (auth, validation)
│   └── utils/               # Utilities, helpers
│
└── shared/                  # Shared types, constants
    ├── types/
    └── constants/
```

## Architecture Decisions

### ADR-001: [Decision Title] (YYYY-MM-DD)
**Decision**: [What was decided]
**Why**: [Reasoning for the decision]
**Alternatives**: [What else was considered]
**Trade-offs**: [Pros and cons of chosen approach]

Example:
### ADR-001: JWT Authentication (2024-01-15)
**Decision**: JWT tokens stored in httpOnly cookies
**Why**: Balance of security (XSS protection) and simplicity
**Alternatives**: Session storage (more complex), localStorage (insecure)
**Trade-offs**: Need CORS configuration, slightly more complex than sessions

## Code Conventions

### Naming
- **Files**: kebab-case (`user-auth.ts`)
- **Components**: PascalCase (`UserProfile.tsx`)
- **Functions/Variables**: camelCase (`getUserById`)
- **Constants**: UPPER_SNAKE_CASE (`API_BASE_URL`)
- **CSS Classes**: kebab-case (`user-profile-card`)
- **Database**: snake_case tables, camelCase in code

### API Design
- **Routes**: `/api/v1/{resource}` (RESTful)
- **Status Codes**:
  - 200 (success), 201 (created), 204 (no content)
  - 400 (bad request), 401 (unauthorized), 403 (forbidden), 404 (not found)
  - 500 (server error), 503 (service unavailable)
- **Error Response Format**:
```json
{
  "error": "Human-readable message",
  "code": "MACHINE_READABLE_CODE",
  "details": { "field": "validation error" }
}
```

### Frontend Patterns
- **State Management**:
  - Local state: `useState` for component-specific
  - Global state: Context API for auth, theme
  - Server state: React Query for API data
- **API Calls**: Custom hooks (`useAuth`, `useSubscription`)
- **Forms**: React Hook Form + Zod validation
- **Styling**: Tailwind utility classes, no custom CSS files
- **Component Structure**:
  - Keep components small (<100 lines preferred)
  - Extract logic to custom hooks
  - Co-locate tests with components

### Backend Patterns
- **Controller Pattern**: Thin controllers, delegate to services
  ```typescript
  // Controller (thin)
  async createUser(req, res) {
    const user = await userService.create(req.body);
    res.status(201).json(user);
  }
  ```
- **Service Pattern**: Business logic, return data or throw
  ```typescript
  // Service (fat)
  async create(data) {
    this.validate(data);
    const hashedPassword = await this.hashPassword(data.password);
    return await this.userRepo.create({ ...data, password: hashedPassword });
  }
  ```
- **Error Handling**: Custom error classes, middleware catches
- **Validation**: Zod schemas for all inputs, validate at API boundary
- **Database**: Prisma ORM, migrations for all schema changes

### Testing Standards
- **Unit Tests**: Business logic in services (aim for 80% coverage)
- **Integration Tests**: API endpoints (request → response)
- **E2E Tests**: Critical user flows only (auth, checkout, onboarding)
- **Test Location**: Co-located with implementation (`auth.ts` → `auth.test.ts`)
- **Test Naming**: `describe('what')` + `it('should do what when condition')`

### File Organization
- **Max File Size**: 300 lines (split if larger)
- **Imports Order**: 1) External, 2) Internal, 3) Types, 4) Styles
- **Exports**: Named exports for utilities, default for components

## Critical File Pointers

### Authentication
- **JWT Service**: `/src/server/services/auth.ts`
- **Auth Middleware**: `/src/server/middleware/auth.ts`
- **User Model**: `/src/server/models/user.ts`
- **Auth Hook**: `/src/client/hooks/useAuth.ts`

### API & Data
- **API Client**: `/src/client/lib/api.ts`
- **Database Schema**: `/src/server/prisma/schema.prisma`
- **Migrations**: `/src/server/prisma/migrations/`

### UI Components
- **Base Components**: `/src/client/components/ui/` (shadcn/ui)
- **Feature Components**: `/src/client/components/features/`
- **Component Specs**: `/design_docs/components/` (Designer creates here)

### Configuration
- **Environment**: `.env.example` (template), `.env` (local, not in git)
- **TypeScript**: `tsconfig.json`
- **Vite**: `vite.config.ts`
- **Tailwind**: `tailwind.config.js`

---

**Update Triggers**:
- Major architectural change (e.g., switching databases)
- New convention established (e.g., new error handling pattern)
- Directory structure changes
- Critical file locations change

**Who Updates**: Any agent can update, PM reviews for consistency

**Max Size**: 500 lines (enforce with quarterly reviews)
