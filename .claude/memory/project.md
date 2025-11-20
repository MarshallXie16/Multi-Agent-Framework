# Project Memory

**Max Size**: 500 lines | **Update**: When architecture/conventions change

---

## What We're Building

[One paragraph: What is this product and who is it for?]

Example: A SaaS platform for [target users] to [main functionality], solving [key problem].

---

## Tech Stack

- **Frontend**: [Framework + Language] (e.g., React 18 + TypeScript)
- **Backend**: [Framework + Language] (e.g., Node.js + Express)
- **Database**: [Database] (e.g., PostgreSQL 15)
- **ORM**: [ORM if applicable] (e.g., Prisma, SQLAlchemy)
- **Auth**: [Auth method] (e.g., JWT, OAuth)
- **Hosting**: [Platforms] (e.g., Vercel + Railway)
- **Testing**: [Test frameworks] (e.g., Jest + Playwright)

---

## Directory Structure

```
[Your actual project structure]

Example:
src/
├── client/               # Frontend
│   ├── components/      # UI components
│   ├── hooks/           # Custom hooks
│   ├── pages/           # Route pages
│   └── lib/             # Utilities
├── server/              # Backend
│   ├── api/             # Route handlers
│   ├── services/        # Business logic
│   ├── models/          # Database models
│   └── middleware/      # Express middleware
└── shared/              # Shared types/constants
```

---

## Code Conventions

### Naming
- **Files**: [convention] (e.g., kebab-case)
- **Components**: [convention] (e.g., PascalCase)
- **Functions**: [convention] (e.g., camelCase)
- **Constants**: [convention] (e.g., UPPER_SNAKE_CASE)
- **Database**: [convention] (e.g., snake_case tables)

### API Design
- **Routes**: [pattern] (e.g., `/api/v1/{resource}`)
- **Status Codes**: [list common ones]
- **Error Format**: [your error response format]

### Frontend Patterns
- **State**: [how you manage state] (e.g., Context for auth, React Query for API)
- **Forms**: [form library] (e.g., React Hook Form + Zod)
- **Styling**: [approach] (e.g., Tailwind CSS)

### Backend Patterns
- **Controllers**: [pattern] (e.g., Thin controllers, fat services)
- **Validation**: [approach] (e.g., Zod schemas at API boundary)
- **Error Handling**: [pattern] (e.g., Custom error classes + middleware)

---

## Architecture Decisions

### ADR-001: [Decision Title] (YYYY-MM-DD)
**Decision**: [What was decided]
**Why**: [Reasoning]
**Alternatives**: [What else was considered]
**Trade-offs**: [Pros and cons]

[Add more ADRs as you make architectural decisions]

---

## Critical File Pointers

[Link to important files with brief descriptions]

Example:
- **Auth Service**: `/src/server/services/auth.ts` - JWT generation and validation
- **User Model**: `/src/server/models/user.ts` - User database schema
- **API Client**: `/src/client/lib/api.ts` - Axios wrapper for API calls
- **Database Schema**: `/prisma/schema.prisma` - Complete data model

---

**Who Updates**: Any agent (PM reviews for consistency)
**When to Update**: Architecture changes, new conventions, directory restructure
