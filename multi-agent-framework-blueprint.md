# Multi-Agent Framework for Autonomous SaaS Development

**Version**: 1.0  
**Purpose**: Enable specialized AI agents to collaboratively build a SaaS product with minimal coordination overhead

---

## Table of Contents

1. [Design Philosophy](#1-design-philosophy)
2. [Agent Roster](#2-agent-roster)
3. [File Structure](#3-file-structure)
4. [Memory System](#4-memory-system)
5. [Task Management](#5-task-management)
6. [Communication System](#6-communication-system)
7. [Workflows & Processes](#7-workflows--processes)
8. [Agent-Specific Behaviors](#8-agent-specific-behaviors)
9. [System Prompt Architecture](#9-system-prompt-architecture)
10. [Implementation Checklist](#10-implementation-checklist)
11. [Quick Reference](#11-quick-reference)

---

## 1. Design Philosophy

### Core Principles

**Minimal Overhead, Maximum Autonomy**
- Agents spend 90% of time coding, 10% on coordination
- Communication only when blocked or need decisions
- Memory and task management combined (one file = one context unit)
- Read codebase directly instead of asking questions

### Problem-Solution Mapping

| Problem | Solution |
|---------|----------|
| **Lack of Direction** | PM maintains backlog/sprint, agents pull clear tasks with acceptance criteria |
| **Context Management** | 3-level memory (project → story → task), progressive loading, one file per unit |
| **Quality Control** | Mandatory code review, curated lessons, flexible ownership with documentation |

---

## 2. Agent Roster

### Core Team (7 Agents)

| Agent | Model | Primary Responsibility | Can Modify Code |
|-------|-------|------------------------|-----------------|
| Product Manager | Sonnet | Task decomposition, sprint planning, coordination | No |
| UI/UX Designer | Sonnet | Component specs, design system, audits | No |
| Frontend Developer | Sonnet | UI implementation, client logic, testing | Yes |
| Backend Developer | Sonnet | API, database, business logic, testing | Yes |
| Code Reviewer | Opus | Quality gate, can make minor fixes | **Yes** (minor fixes only) |
| Debugger | Opus | Deep investigation, root cause analysis | Yes |
| DevOps Engineer | Sonnet | CI/CD, deployment, infrastructure | Yes |

**Key Distinctions**:
- **Code Reviewer**: Can make minor fixes (typos, formatting, simple bugs) to avoid ping-pong. For major issues (architecture problems, significant refactors), requests changes from implementer.
- **Ownership**: All agents can read all files. Agents can modify files outside their domain if justified and documented.

---

## 3. File Structure

```
project-root/
├── design_docs/                    # Static foundation (rarely changes)
│   ├── business_plan.md
│   ├── technical_requirements.md
│   ├── product_design.md
│   ├── user_stories.md
│   ├── roadmap.md
│   └── components/                 # Component specs (Designer creates)
│       ├── subscription-card.md
│       ├── dashboard-widget.md
│       └── ...
│
├── .claude/                        # Agent coordination hub
│   ├── CLAUDE.md                   # Shared base system prompt
│   │
│   ├── agents/                     # Subagent definitions
│   │   ├── product-manager.md
│   │   ├── ui-ux-designer.md
│   │   ├── frontend-developer.md
│   │   ├── backend-developer.md
│   │   ├── code-reviewer.md
│   │   ├── debugger.md
│   │   └── devops-engineer.md
│   │
│   ├── workflows/                  # Process definitions
│   │   ├── sprint-planning.md
│   │   ├── code-review-checklist.md
│   │   ├── debugging-protocol.md
│   │   └── deployment-checklist.md
│   │
│   ├── memory/
│   │   ├── project.md              # Project overview + conventions
│   │   ├── lessons.md              # Curated past mistakes & solutions
│   │   └── backlog.md              # Future work (structured tickets)
│   │
│   ├── active/                     # Current sprint only
│   │   ├── sprint.md               # Index of stories & tasks
│   │   ├── stories/                # User story files
│   │   │   ├── US001-auth.md
│   │   │   ├── US002-billing.md
│   │   │   └── ...
│   │   └── tasks/                  # Task files
│   │       ├── T001-login-api.md
│   │       ├── T002-signup-ui.md
│   │       └── ...
│   │
│   ├── communication/
│   │   └── chat.jsonl              # Group chat (JSONL format)
│   │
│   └── archive/                    # Completed work
│       ├── sprint-01/
│       │   ├── stories/
│       │   └── summary.md
│       ├── sprint-02/
│       └── ...
│
├── src/                            # Source code
│   ├── client/                     # Frontend code
│   │   ├── components/
│   │   │   ├── ui/                 # Base UI components (shadcn/ui)
│   │   │   └── features/           # Feature-specific components
│   │   ├── pages/
│   │   ├── hooks/
│   │   ├── lib/
│   │   └── types/
│   ├── server/                     # Backend code
│   │   ├── api/                    # Route handlers
│   │   ├── services/               # Business logic
│   │   ├── models/                 # Database models
│   │   ├── middleware/
│   │   └── utils/
│   └── shared/                     # Shared code (types, constants)
│
└── docs/                           # User-facing documentation
    ├── api/
    ├── guides/
    └── README.md
```

---

## 4. Memory System

### Overview: 3-Level Hierarchy

```
Project Memory (Static, loads once)
    ↓
Story Memory (Persists across feature)
    ↓
Task Memory (Session-scoped, then summarized)
```

---

### Level 1: Project Memory

**File**: `.claude/memory/project.md`

**Purpose**: Everything you need to understand the project. Loaded once per session.

**Max Size**: 500 lines (enforce with quarterly reviews)

**Template**:

```markdown
# Project Memory

## What We're Building
[One paragraph - elevator pitch]

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

### ADR-001: JWT Authentication (2024-01-15)
**Decision**: JWT tokens stored in httpOnly cookies  
**Why**: Balance of security (XSS protection) and simplicity  
**Alternatives**: Session storage (more complex), localStorage (insecure)  
**Trade-offs**: Need CORS configuration, slightly more complex than sessions

### ADR-002: Monorepo (2024-01-10)
**Decision**: Single repo for frontend + backend  
**Why**: Shared types, atomic commits, simpler for small team  
**Trade-offs**: Slightly more complex deployment (worth it for DX)

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
  - Keep components small (<150 lines)
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
```

**Update Triggers**:
- Major architectural change (e.g., switching databases)
- New convention established (e.g., new error handling pattern)
- Directory structure changes
- Critical file locations change

**Who Updates**: Any agent can update, PM reviews for consistency

---

### Level 2: Story Memory

**Location**: `.claude/active/stories/US###-name.md`

**Purpose**: Complete context for a user story. Contains requirements, decisions, progress, blockers.

**Template**:

```markdown
# US001: User Authentication System

## Story Definition
**As a** user  
**I want to** create an account and log in  
**So that** I can access personalized features

## Acceptance Criteria
- [ ] User can sign up with email/password
- [ ] User can log in with credentials
- [ ] Session persists across browser refreshes
- [ ] User can log out
- [ ] Password meets security requirements (8+ chars, complexity)
- [ ] Rate limiting prevents brute force attacks

## Technical Approach
**Auth Method**: JWT with httpOnly cookies  
**Why**: Secure (XSS-proof), stateless (scalable), industry standard  
**Security**: 
- bcrypt for passwords (salt rounds: 10)
- Rate limiting (5 attempts per 15 min)
- JWT expiry (7 days)

## Tasks Breakdown
- [x] T001: Backend - User model & migration (Backend Dev)
- [x] T002: Backend - JWT service (Backend Dev)
- [x] T003: Backend - /signup endpoint (Backend Dev)
- [x] T004: Backend - /login endpoint (Backend Dev)
- [x] T005: Frontend - useAuth hook (Frontend Dev)
- [x] T006: Frontend - Login page (Frontend Dev)
- [x] T007: Frontend - Signup page (Frontend Dev)
- [x] T008: E2E tests (Frontend Dev)
- [x] T009: Code review (Code Reviewer)

## Architecture

### Backend Files
- `/src/server/models/user.ts` - User schema (Prisma)
- `/src/server/services/auth.ts` - JWT logic, password hashing
- `/src/server/api/auth.ts` - Auth routes
- `/src/server/middleware/auth.ts` - Auth middleware

### Frontend Files
- `/src/client/hooks/useAuth.ts` - Auth state hook
- `/src/client/pages/Login.tsx` - Login UI
- `/src/client/pages/Signup.tsx` - Signup UI
- `/src/client/lib/api.ts` - API client with auth headers

### Database Schema
```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  name VARCHAR(255),
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

CREATE INDEX idx_users_email ON users(email);
```

## Key Decisions & Context

### Decision: Rate Limiting Strategy
- **What**: 5 login attempts per 15 minutes per IP
- **Why**: Balance security and UX (not too restrictive)
- **Implementation**: express-rate-limit middleware
- **Alternative Considered**: Account-based limiting (requires database lookup)

### Decision: Password Requirements
- **What**: Min 8 chars, must contain uppercase, lowercase, number
- **Why**: OWASP recommendations, industry standard
- **Implementation**: Zod validator, enforced on backend
- **Frontend**: Show requirements in real-time as user types

### Context: CORS Configuration
- **Dev Environment**: Frontend at localhost:5173, backend at localhost:3000
- **Solution**: CORS middleware with credentials: true, origin: localhost:5173
- **Production**: Same domain, different paths (/api proxied by Vercel)

## Progress Log

### 2024-01-15 - Backend Developer (Session 1)
- Created user model with Prisma schema
- Implemented migration (users table)
- Built JWT service (sign, verify, decode)
- Implemented /signup endpoint with validation
- **Tests**: All unit tests passing (8/8)
- **Next**: Implement /login endpoint

### 2024-01-15 - Backend Developer (Session 2)
- Implemented /login endpoint
- Added rate limiting (express-rate-limit)
- Fixed password validation edge case (empty string)
- **Tests**: All integration tests passing (6/6)
- **Next**: Frontend can integrate

### 2024-01-16 - Frontend Developer (Session 1)
- Created useAuth hook (login, signup, logout, refresh)
- Implemented Login page with form validation
- Integrated with backend API (tested manually)
- **Issue**: CORS errors (DevOps to fix)
- **Next**: Signup page

### 2024-01-16 - DevOps Engineer
- Fixed CORS configuration (added credentials support)
- **Resolution**: Frontend can now make authenticated requests

### 2024-01-16 - Frontend Developer (Session 2)
- Implemented Signup page
- Added protected route wrapper (<ProtectedRoute>)
- E2E test: signup → login → protected page ✓
- **Next**: Code review

### 2024-01-17 - Code Reviewer
- Reviewed all auth code
- **Minor Fixes Made**: Formatting, typo in error message
- **Requested Changes**: Add error boundary around auth forms
- **Status**: Approved after Frontend adds error boundary

### 2024-01-17 - Frontend Developer (Session 3)
- Added error boundary component
- **Status**: All changes complete, ready to merge

## Blockers & Resolutions

### [2024-01-16] Frontend → DevOps
**Blocker**: CORS errors when calling /login from localhost:5173  
**Resolution**: DevOps added CORS middleware with credentials support  
**Time Lost**: 1 hour

## Status
- **Implementation**: Complete ✓
- **Code Review**: Approved ✓
- **Testing**: E2E tests passing ✓
- **Documentation**: API docs updated ✓
- **READY TO ARCHIVE**

## Key Learnings (for lessons.md)
1. JWT secret must be in .env, never hardcoded (security)
2. Always validate inputs on backend, even if frontend validates
3. httpOnly cookies prevent XSS but require CORS credentials config
4. Prisma unique constraint errors: catch P2002 error code explicitly
```

**Lifecycle**:
1. PM creates when story starts
2. Agents update progress log after each session
3. Agents add key decisions/context as discovered
4. When complete: PM extracts lessons → `lessons.md`, moves to archive

---

### Level 3: Task Memory

**Location**: `.claude/active/tasks/T###-description.md`

**Purpose**: Full context for ONE task. Task memory lives here during work, gets summarized to story memory when done.

**Template**:

```markdown
# T003: Implement /signup Endpoint

## Assignment
**Story**: US001 - User Authentication  
**Assigned To**: Backend Developer  
**Priority**: P1 (High)  
**Estimate**: 3 hours  
**Status**: Complete

## Requirements
Implement user signup endpoint:
- Accept email, password, name (optional)
- Validate email format and uniqueness
- Validate password strength
- Hash password with bcrypt
- Create user in database
- Return JWT token on success

## Acceptance Criteria
- [ ] POST /api/v1/auth/signup accepts { email, password, name? }
- [ ] Email validation: valid format, unique in database
- [ ] Password validation: min 8 chars, complexity rules
- [ ] Password hashed with bcrypt (salt rounds: 10)
- [ ] Returns 201 with { user, token } on success
- [ ] Returns 400 for validation errors
- [ ] Returns 409 for duplicate email
- [ ] Unit tests cover happy path and errors
- [ ] Integration test covers full endpoint flow

## Context & References
- User model exists from T001
- JWT service available from T002
- Follow API error format from project.md
- See design_docs/technical_requirements.md for security standards

---

## Task Memory (Investigation & Implementation)

### Investigation (2024-01-15 09:00)
Checking existing code:
- ✓ User model has email, password_hash, name, timestamps
- ✓ JWT service has signToken(userId, email) method
- ✓ Found email validator in /src/server/utils/validators.ts (can reuse)
- ✓ bcrypt already installed (v5.1.0)

Decisions:
- Use Zod for request validation (consistent with project standards)
- Password regex: /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d).{8,}$/ (min 8, upper, lower, number)

### Implementation Plan (09:30)
1. Create Zod schema for signup request
2. Implement signup service function:
   - Validate input
   - Check email uniqueness
   - Hash password
   - Create user in DB
   - Generate JWT
3. Create POST /signup route handler
4. Write unit tests for service
5. Write integration test for endpoint

### Implementation (10:00)
Created files:
- `/src/server/api/auth.ts` - Route handlers
- `/src/server/services/auth.ts` - Business logic

Key implementation details:
```typescript
// Zod schema
const SignupSchema = z.object({
  email: z.string().email(),
  password: z.string().min(8).regex(/^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)/),
  name: z.string().optional()
});

// Service function
async signup(data) {
  // 1. Validate
  const validated = SignupSchema.parse(data);
  
  // 2. Check uniqueness
  const existing = await prisma.user.findUnique({ 
    where: { email: validated.email } 
  });
  if (existing) throw new ConflictError('Email already registered');
  
  // 3. Hash password
  const password_hash = await bcrypt.hash(validated.password, 10);
  
  // 4. Create user
  const user = await prisma.user.create({
    data: { ...validated, password_hash }
  });
  
  // 5. Generate token
  const token = jwt.signToken(user.id, user.email);
  
  return { user, token };
}
```

### Issues Encountered (11:00)

#### Issue 1: Prisma Unique Constraint Error Not Caught
**Problem**: When duplicate email, Prisma throws PrismaClientKnownRequestError but wasn't being caught properly  
**Solution**: Check error code explicitly
```typescript
try {
  const user = await prisma.user.create({ data });
} catch (error) {
  if (error.code === 'P2002') {
    throw new ConflictError('Email already registered');
  }
  throw error;
}
```

#### Issue 2: Password Validation Edge Case
**Problem**: Empty string passed password regex  
**Solution**: Added `.min(8)` to Zod schema catches this before regex

### Testing (11:30)

**Unit Tests** (`/src/server/services/auth.test.ts`):
- ✓ Valid signup returns user + token
- ✓ Invalid email throws ValidationError
- ✓ Weak password throws ValidationError
- ✓ Duplicate email throws ConflictError
- ✓ Password is hashed (not stored plain text)
- ✓ Token is valid JWT

**Integration Test** (`/src/server/api/auth.test.ts`):
- ✓ POST /signup with valid data returns 201 + user + token
- ✓ POST /signup with invalid email returns 400
- ✓ POST /signup with weak password returns 400
- ✓ POST /signup with duplicate email returns 409
- ✓ Response token can be decoded and verified
- ✓ User password_hash not in response (security)

**Manual Testing**:
```bash
# Success case
curl -X POST http://localhost:3000/api/v1/auth/signup \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"Test1234","name":"Test User"}'

# Returns: {"user":{"id":1,"email":"test@example.com","name":"Test User"},"token":"eyJ..."}
```

---

## Deliverables
- ✓ `/src/server/services/auth.ts` (signup function)
- ✓ `/src/server/api/auth.ts` (POST /signup handler)
- ✓ `/src/server/services/auth.test.ts` (unit tests)
- ✓ `/src/server/api/auth.test.ts` (integration tests)
- ✓ All tests passing (12/12)

## Summary for Story Memory
Implemented /signup endpoint with email/password validation, bcrypt hashing (salt rounds: 10), and duplicate email checks. Returns JWT token on success. All unit and integration tests passing. 

**Key Learning**: Prisma unique constraint errors need explicit handling - check error.code === 'P2002' for duplicate violations.

**API Ready**: Frontend can now integrate signup flow.

## Next Steps
- Frontend: Implement signup page (T007)
- Backend: Implement /login endpoint (T004)

## Status
**Complete** - Ready for code review (2024-01-15 12:00)
```

**Lifecycle**:
1. Agent picks task from sprint.md
2. Agent works, adding to "Task Memory" section
3. Agent completes, writes summary
4. PM copies summary to story file
5. PM deletes task file (context preserved in story)

---

## 5. Task Management

### Backlog (Future Work)

**File**: `.claude/memory/backlog.md`

**Purpose**: Structured queue of ready-to-work tickets. Not prioritized or assigned yet.

**Format**: Each item is a mini-ticket with all necessary context

```markdown
# Backlog

## Features

### F001: Password Reset via Email
**Priority**: P2 (Medium)  
**Estimate**: 5 hours  
**Description**: Allow users to reset password via email link  
**Acceptance Criteria**:
- User can request reset link at /forgot-password
- Email sent with time-limited token (1 hour expiry)
- User can set new password at /reset-password?token=xxx
- Old password invalidated after reset
**Technical Notes**:
- Need email service (SendGrid? Resend?)
- Store reset tokens in database with expiry
- Rate limit reset requests (prevent abuse)
**Dependencies**: None  
**References**: design_docs/user_stories.md (US015)

### F002: Social Login (Google OAuth)
**Priority**: P2 (Medium)  
**Estimate**: 8 hours  
**Description**: Allow login with Google account  
**Acceptance Criteria**:
- "Continue with Google" button on login/signup
- OAuth flow completes and creates/authenticates user
- Email from Google account linked to user record
**Technical Notes**:
- Use Passport.js or similar OAuth library
- Need Google Cloud Console app setup
- Handle case where Google email already exists
**Dependencies**: None  
**References**: design_docs/user_stories.md (US016)

---

## Bugs

### B001: Login Page - "Remember Me" Not Working
**Priority**: P1 (High)  
**Estimate**: 2 hours  
**Description**: "Remember me" checkbox doesn't extend session  
**Steps to Reproduce**:
1. Go to /login
2. Check "Remember me"
3. Log in
4. Close browser, reopen
5. Session expired (should persist 30 days if checked)
**Expected**: Session persists 30 days when "remember me" checked  
**Actual**: Session expires after 7 days regardless  
**Technical Notes**:
- Issue likely in JWT expiry logic
- Need to pass "remember" flag to auth service
- Two token expiries: 7 days (default), 30 days (remember)
**References**: Reported by user, Issue #45

### B002: Dashboard Chart Not Rendering on Mobile Safari
**Priority**: P2 (Medium)  
**Estimate**: 3 hours  
**Description**: Usage chart shows blank on iOS Safari, works on desktop  
**Steps to Reproduce**:
1. Log in on iPhone Safari
2. Go to /dashboard
3. Chart area is blank (no error)
**Technical Notes**:
- Suspected CSS/Canvas issue
- Check if recharts supports Safari
- Test on iOS simulator
**References**: Reported by 3 users, Issue #52

---

## Technical Debt

### TD001: Refactor Auth Service (Too Many Responsibilities)
**Priority**: P3 (Low)  
**Estimate**: 4 hours  
**Description**: auth.ts service has 500+ lines, doing too much  
**Why**: Hard to test, hard to understand, violates SRP  
**Proposed Solution**:
- Split into: auth.ts (orchestration), password.ts (hashing/validation), jwt.ts (token logic), user.ts (user CRUD)
- Keep public interface same (no breaking changes)
**Benefit**: Better testability, easier to maintain  
**Risk**: Low (refactor only, no behavior change)

### TD002: Add Database Indexes for Common Queries
**Priority**: P2 (Medium)  
**Estimate**: 3 hours  
**Description**: Queries on users.email, subscriptions.user_id slow as data grows  
**Why**: Missing indexes on frequently queried columns  
**Proposed Solution**:
- Add index on users.email (for login lookups)
- Add index on subscriptions.user_id (for dashboard queries)
- Add composite index on subscriptions(user_id, status)
**Benefit**: Faster queries (currently 200ms → expected <50ms)  
**References**: Performance monitoring data

---

## Optimizations

### O001: Add Redis Caching for User Sessions
**Priority**: P3 (Low)  
**Estimate**: 6 hours  
**Description**: Cache user data in Redis to reduce database hits  
**Current**: Every request validates JWT → fetches user from DB  
**Proposed**: Cache user in Redis (key: userId, TTL: 1 hour)  
**Benefit**: Reduce DB load by ~70%, faster response times  
**Cost**: Need Redis instance (~$10/month)  
**References**: Performance monitoring shows 1000+ user queries/minute

### O002: Lazy Load Dashboard Components
**Priority**: P2 (Medium)  
**Estimate**: 3 hours  
**Description**: Dashboard loads all widgets upfront (slow initial load)  
**Current**: 5 widgets loaded synchronously (bundle size: 800KB)  
**Proposed**: React.lazy() for below-fold widgets  
**Benefit**: Faster initial render (bundle: 800KB → 300KB initial)  
**References**: Lighthouse score: 65 → expected 85+
```

**Management**:
- Any agent can add items (structured format required)
- PM reviews weekly, pulls into sprints
- PM updates priorities based on user feedback, urgency

**Format Requirements**:
- Each item has: ID, Priority, Estimate, Description, Acceptance Criteria or Technical Notes
- Features reference design docs
- Bugs have repro steps
- Technical debt explains why and proposed solution

---

### Sprint (Current Work)

**File**: `.claude/active/sprint.md`

**Purpose**: Index of current sprint work. One-line summaries pointing to files. Quick scan for status.

**Template**:

```markdown
# Sprint 3 (Feb 1-14, 2025)

## Goal
Complete subscription billing and user dashboard MVP

## User Stories

### US001: User Authentication
**Status**: ✅ Complete  
**File**: archive/sprint-01/US001-auth.md  
**Completed**: 2024-01-17

### US002: Subscription Plans
**Status**: 🚧 In Progress  
**File**: stories/US002-billing.md  
**Progress**: 6/8 tasks complete

### US003: User Dashboard
**Status**: 📋 Not Started  
**File**: stories/US003-dashboard.md  
**Progress**: 0/5 tasks

---

## Active Tasks

### US002: Subscription Plans

#### T009: Backend - Stripe Integration ✅
**Assigned**: Backend Developer  
**Status**: Complete  
**Completed**: 2024-01-20

#### T010: Backend - Webhook Handler 🚧
**Assigned**: Backend Developer  
**Status**: In Progress  
**Started**: 2024-01-21  
**Blocker**: ❌ Waiting for user to provide Stripe test API keys

#### T011: Frontend - Pricing Page ✅
**Assigned**: Frontend Developer  
**Status**: Complete  
**Completed**: 2024-01-20

#### T012: Frontend - Checkout Flow 🚧
**Assigned**: Frontend Developer  
**Status**: In Progress  
**Started**: 2024-01-21  
**ETA**: 2024-01-22

#### T013: E2E - Full Subscription Flow 📋
**Assigned**: Frontend Developer  
**Status**: Waiting (blocked by T010)  
**Dependency**: T010 must complete first

#### T014: Code Review 📋
**Assigned**: Code Reviewer  
**Status**: Not Started (waiting for T012)

### US003: User Dashboard

#### T015: Frontend - Dashboard Layout 📋
**Assigned**: Frontend Developer  
**Status**: Not Started  
**Estimate**: 3 hours

#### T016: Backend - Usage Analytics API 📋
**Assigned**: Backend Developer  
**Status**: Not Started  
**Estimate**: 5 hours

---

## Priorities
- **P0 (Critical/Blocker)**: T010 (unblock billing)
- **P1 (High)**: T012, T015, T016
- **P2 (Medium)**: T013, T014

## Size Estimates
- **Small (1-3h)**: T015
- **Medium (4-6h)**: T012, T016
- **Large (7-10h)**: T013

## Blockers
- **T010**: Waiting on user to provide Stripe test API keys (sent request 2024-01-21)
- **T013**: Depends on T010 completion

## Velocity Tracking
- **Completed This Sprint**: 4 tasks (T009, T011, +2 from US001)
- **Target**: 10 tasks per sprint
- **On Track**: Yes (40% complete, 30% through sprint)

## Notes
- Stripe webhook testing requires ngrok or deployed environment
- Dashboard design specs in design_docs/components/dashboard-*.md
```

**Agent Workflow**:
1. Check sprint.md for assigned tasks
2. Pick highest priority unblocked task
3. Read task file to get full context
4. Work on task
5. Update sprint.md status (In Progress → Complete)

**PM Workflow**:
1. Update daily (review agent progress)
2. Identify and resolve blockers
3. Assign new tasks as agents finish
4. Track velocity for future planning
5. End of sprint: Delete sprint.md, archive stories

**Status Emojis**:
- ✅ Complete
- 🚧 In Progress
- 📋 Not Started
- ❌ Blocked
- ⏸️ Paused

---

## 6. Communication System

### Design Principles

**Communicate Only When**:
1. **Blocked** by another agent's work
2. **Important decision** needed (multi-domain or requires user input)

**Don't Communicate For**:
- Implementation details (agents decide)
- Minor decisions (agents have autonomy)
- Status updates (use sprint.md)
- Questions answerable by reading code

---

### Group Chat (JSONL Format)

**File**: `.claude/communication/chat.jsonl`

**Why JSONL** (JSON Lines, not Markdown):
- Agents filter without reading entire file
- Machine-parseable metadata
- Easy to append (one line per message)
- Easy to summarize (compress old messages)

**Message Structure**:
```json
{
  "id": "msg_001",
  "timestamp": "2024-01-15T10:30:00Z",
  "sender": "backend-developer",
  "recipients": ["frontend-developer"],
  "type": "handoff",
  "subject": "Auth API Ready",
  "message": "Implemented /signup and /login endpoints. API contract: email/password input, returns user + JWT token. Rate limited to 5 attempts per 15min. Ready for frontend integration.",
  "references": ["US001", "T003", "T004"],
  "priority": "normal"
}
```

**Message Types**:
- `handoff` - Work complete, another agent can start
- `blocker` - I'm blocked, need help
- `question` - Need information from another agent
- `decision` - Need group decision
- `decision-final` - Decision made by PM
- `response` - Reply to another message
- `resolution` - Problem solved
- `info` - General information (use sparingly)

**Priority Levels**:
- `critical` - Blocks all work, need immediate attention
- `high` - Blocks significant work
- `normal` - Standard communication
- `low` - FYI, no action needed

---

### Reading Chat (Efficient Filtering)

**Pseudocode for Agent Session Start**:
```python
def get_my_messages(my_role):
    messages = []
    cutoff = now() - timedelta(hours=24)
    
    with open('.claude/communication/chat.jsonl', 'r') as f:
        for line in f:
            msg = json.loads(line)
            
            # Filter: addressed to me (or "all") AND recent
            if (my_role in msg['recipients'] or 'all' in msg['recipients']) and \
               parse_timestamp(msg['timestamp']) > cutoff:
                messages.append(msg)
    
    return messages
```

**Agent Reads**:
- Messages to them or "all"
- Last 24 hours only
- Can query by references (e.g., all messages about US001)

---

### Communication Examples

**Example 1: Blocker (Backend Blocked by Missing Env Var)**
```json
{
  "id": "msg_015",
  "timestamp": "2024-01-21T14:30:00Z",
  "sender": "backend-developer",
  "recipients": ["devops-engineer", "product-manager"],
  "type": "blocker",
  "subject": "T010 Blocked - Missing Stripe Keys",
  "message": "Cannot complete webhook handler implementation. Need Stripe test API keys (STRIPE_SECRET_KEY and STRIPE_WEBHOOK_SECRET). Created task T999 in backlog for DevOps to add to .env. Blocked until resolved.",
  "references": ["US002", "T010"],
  "priority": "high"
}
```

**Example 2: Decision (Frontend + Backend Disagree on Approach)**
```json
{
  "id": "msg_016",
  "timestamp": "2024-01-22T09:00:00Z",
  "sender": "frontend-developer",
  "recipients": ["backend-developer", "product-manager"],
  "type": "decision",
  "subject": "Dashboard API Design - REST vs GraphQL",
  "message": "For dashboard (US003), should we use REST or GraphQL? Backend suggests REST (5 endpoints). I prefer GraphQL (single endpoint, frontend flexibility). Tradeoffs: REST = simpler, more requests. GraphQL = complex setup, single request. Need decision to proceed.",
  "references": ["US003", "T015", "T016"],
  "priority": "normal"
}
```

**Example 3: Decision Response (Backend Gives Input)**
```json
{
  "id": "msg_017",
  "timestamp": "2024-01-22T09:15:00Z",
  "sender": "backend-developer",
  "recipients": ["product-manager"],
  "type": "response",
  "subject": "Re: Dashboard API Design",
  "message": "I vote REST for MVP. Reasons: (1) Team knows REST well, (2) Dashboard has clear entity boundaries (widgets), (3) GraphQL adds complexity for minimal benefit at our scale. Can revisit if API requests become bottleneck.",
  "references": ["US003", "msg_016"],
  "priority": "normal"
}
```

**Example 4: Decision Final (PM Makes Call)**
```json
{
  "id": "msg_018",
  "timestamp": "2024-01-22T10:00:00Z",
  "sender": "product-manager",
  "recipients": ["frontend-developer", "backend-developer"],
  "type": "decision-final",
  "subject": "Decision: Use REST for Dashboard API",
  "message": "Going with REST for US003. Reasoning: MVP speed > optimization. If we hit performance issues post-launch, we can refactor to GraphQL. Backend: design 5 endpoints. Frontend: implement with existing API client. Document decision in US003 story memory.",
  "references": ["US003", "msg_016"],
  "priority": "normal"
}
```

**Example 5: Handoff (Backend to Frontend)**
```json
{
  "id": "msg_019",
  "timestamp": "2024-01-22T16:00:00Z",
  "sender": "backend-developer",
  "recipients": ["frontend-developer"],
  "type": "handoff",
  "subject": "Dashboard API Complete - Ready for Integration",
  "message": "Completed T016. Dashboard analytics API ready. 5 endpoints: GET /dashboard/overview, /dashboard/usage, /dashboard/activity, /dashboard/billing, /dashboard/settings. All return JSON. Auth required (JWT). See detailed docs in task T016 file. Integration tests passing. Ready for frontend.",
  "references": ["US003", "T016"],
  "priority": "normal"
}
```

---

### Chat Maintenance

**Weekly Summarization** (PM task):
1. Take messages older than 7 days
2. Create summary:
   ```markdown
   ## Week of Jan 15-21, 2025
   
   **Key Decisions**:
   - US003: Chose REST over GraphQL for dashboard API
   - US002: Approved Stripe webhook implementation approach
   
   **Blockers Resolved**:
   - Stripe API keys added to environment (Jan 21)
   - CORS issues fixed for checkout flow (Jan 19)
   
   **Handoffs**:
   - Backend → Frontend: Auth API (Jan 15)
   - Backend → Frontend: Dashboard API (Jan 22)
   ```
3. Delete old messages
4. Keep last 50 messages (for recent context)
5. Save summary to archive with sprint

---

## 7. Workflows & Processes

### Where Workflows Live

| Workflow Type | Location | How Agents Access |
|---------------|----------|-------------------|
| **Simple Protocols** (session start/end) | CLAUDE.md | Automatically loaded (in system prompt) |
| **Role-Specific Behaviors** | .claude/agents/{role}.md | Automatically loaded (in subagent prompt) |
| **Complex Multi-Step Processes** | .claude/workflows/*.md | Agent reads when needed (prompted to check) |

---

### Workflows in CLAUDE.md (Session Protocols)

These are in the shared base prompt, so all agents follow them automatically.

#### Session Start Protocol

```markdown
## Session Start Protocol (5 minutes)

All agents follow this checklist at session start:

1. **Load Project Context** (1 min)
   - Skim `.claude/memory/project.md` (you know this, just refresh key points)

2. **Check Communications** (1 min)
   - Read `.claude/communication/chat.jsonl`
   - Filter: Last 24 hours + messages to you or "all"
   - Note any blockers or decisions involving you

3. **Check Sprint Status** (1 min)
   - Read `.claude/active/sprint.md`
   - Identify tasks assigned to you
   - Check priorities and blockers

4. **Select Task** (1 min)
   - Choose highest priority unblocked task assigned to you
   - If no tasks: Check with PM via chat or review backlog

5. **Load Task Context** (1 min)
   - Read task file (`.claude/active/tasks/T###-name.md`)
   - Read referenced story file if needed (`.claude/active/stories/US###-name.md`)
   - Skim related code files (progressive loading)

**Ready to work** ✓
```

#### During Session Behaviors

```markdown
## During Session Behaviors

### Progressive Context Loading
- Don't read entire codebase upfront (waste of time)
- Load context as needed: "How does auth work?" → Read auth.ts
- Use grep/search to find relevant files: `grep -r "function login" src/`

### Task Memory Updates
- Add to task file's "Task Memory" section as you work
- Format: Chronological log of investigation → decisions → implementation
- Be concise but include key details (why you chose X over Y)

### Communication
- **If blocked**: Send blocker message + create unblocking task in backlog
- **If need decision**: Send decision message with clear options and tradeoffs
- **If complete handoff**: Update sprint.md + send handoff message (optional)

### Sprint Status Updates
Update `.claude/active/sprint.md` when:
- Starting task: Change to "🚧 In Progress"
- Complete task: Change to "✅ Complete"
- Blocked: Change to "❌ Blocked - [reason]"

### Cross-Domain Work
You can modify files outside your primary domain if:
- Urgent bug blocking your work
- Configuration needed for your feature
- Small fix (< 10 lines)

**Always**: Document why in commit message
- Example: `git commit -m "Backend: Fixed CORS in frontend vite.config - blocked API testing"`
```

#### Session End Protocol

```markdown
## Session End Protocol (5 minutes)

All agents follow this checklist at session end:

1. **Finalize Work** (2 min)
   - Commit code with descriptive message (include task ID)
   - Example: `git commit -m "T012: Implemented checkout flow with Stripe integration"`

2. **Update Task File** (2 min)
   - Fill out "Summary for Story Memory" section
   - Extract key decisions, learnings, blockers
   - Mark deliverables complete

3. **Update Sprint** (30 sec)
   - Update task status in `sprint.md`
   - If complete: ✅, if blocked: ❌ with reason

4. **Communicate if Needed** (30 sec)
   - Handoff: If another agent can now start
   - Blocker: If you're stuck
   - Don't send message for normal progress

5. **Clean Up** (if task complete)
   - PM will copy summary to story memory
   - PM will delete task file
   - You're done ✓

**Session complete** ✓
```

---

### Workflows in Agent Prompts

Role-specific behaviors go in each agent's `.claude/agents/{role}.md` file.

**Example: Code Reviewer's Review Process** (in `code-reviewer.md`):
```markdown
## Code Review Process

### When to Review
- Task marked "Complete" in sprint.md
- Agent sends review request (optional)
- PM assigns you a review task

### Review Steps

1. **Read Context** (5 min)
   - Read story file for acceptance criteria
   - Read task file for implementation details
   - Skim related code changes

2. **Run Tests** (2 min)
   - `npm test` (or equivalent)
   - All tests must pass before review

3. **Review Code** (15-30 min depending on size)
   - Read `.claude/workflows/code-review-checklist.md` (your detailed checklist)
   - Check each item on checklist
   - Note issues in review notes

4. **Make Minor Fixes** (5-10 min)
   - Typos, formatting, simple bugs: Fix directly
   - Document fixes in commit: "Code Review: Fixed typo in error message"

5. **Request Major Changes** (if needed)
   - Add detailed notes to story memory under "Code Review" section
   - Update sprint.md: "🔄 Review - Changes Requested"
   - Send message to implementer with specifics

6. **Approve** (if ready)
   - Update story memory: "Code Review: Approved ✓"
   - Update sprint.md: "✅ Complete"
   - PM will merge and archive

### What You Can Fix vs. Request Changes

**Fix Directly** (minor issues):
- Typos, grammar in comments/strings
- Code formatting (indentation, spacing)
- Missing semicolons, trailing commas
- Simple logic bugs (< 5 lines to fix)
- Missing error handling (add try/catch)

**Request Changes** (major issues):
- Architecture problems (wrong pattern)
- Security vulnerabilities (SQL injection, XSS)
- Performance issues (N+1 queries)
- Missing tests
- Significant logic errors
- Doesn't meet acceptance criteria
```

---

### Workflows in .claude/workflows/ (Complex Processes)

For complex, multi-step processes that don't fit in prompts.

#### Example: `.claude/workflows/code-review-checklist.md`

```markdown
# Code Review Checklist

Use this checklist for every code review. Check each item.

## Functionality
- [ ] Meets all acceptance criteria from story/task
- [ ] Handles expected inputs correctly
- [ ] Handles edge cases (empty, null, invalid input)
- [ ] Error messages are clear and actionable

## Code Quality
- [ ] Functions are small (<50 lines preferred)
- [ ] Functions have single responsibility
- [ ] No code duplication (DRY principle)
- [ ] Descriptive variable/function names
- [ ] Complex logic has comments explaining "why"

## Testing
- [ ] Unit tests for business logic (aim 80% coverage)
- [ ] Integration tests for API endpoints
- [ ] E2E tests for critical user flows (if applicable)
- [ ] All tests passing
- [ ] Tests cover edge cases, not just happy path

## Security (OWASP Top 10)
- [ ] No SQL injection vulnerabilities (use parameterized queries)
- [ ] No XSS vulnerabilities (sanitize inputs, escape outputs)
- [ ] Authentication required for protected endpoints
- [ ] Authorization checks present (user can only access their data)
- [ ] Secrets not hardcoded (use .env)
- [ ] Sensitive data not logged
- [ ] Rate limiting on auth/payment endpoints

## Performance
- [ ] No N+1 query problems (use eager loading)
- [ ] Database queries have indexes
- [ ] No unnecessary API calls
- [ ] Large lists paginated
- [ ] Images/assets optimized

## Conventions (from project.md)
- [ ] Follows naming conventions
- [ ] Follows file structure conventions
- [ ] Follows API design patterns
- [ ] Follows error handling patterns
- [ ] Imports ordered correctly

## Documentation
- [ ] Complex functions have docstrings
- [ ] API endpoints documented
- [ ] README updated if setup changed
- [ ] Lessons added to lessons.md (if applicable)

## Cross-Domain Changes
- [ ] If modified files outside primary domain, reason documented in commit
- [ ] Cross-domain changes are necessary (not convenience)
- [ ] Coordinate with domain owner if significant
```

**How Agent Uses This**:
Code Reviewer's prompt says: "When reviewing, read `.claude/workflows/code-review-checklist.md` and check each item."

---

#### Example: `.claude/workflows/sprint-planning.md`

```markdown
# Sprint Planning Workflow (PM)

Run this at start of each sprint (typically Monday morning).

## Pre-Planning (30 min)

1. **Archive Previous Sprint**
   - Copy completed stories from `active/stories/` to `archive/sprint-##/`
   - Extract key learnings to `lessons.md`
   - Delete old `sprint.md`
   - Create sprint summary in archive

2. **Review Backlog**
   - Read `memory/backlog.md`
   - Identify candidate items for sprint
   - Check dependencies (some items blocked by others)
   - Consider velocity (last sprint: X tasks, target: Y tasks)

3. **Check Roadmap**
   - Read `design_docs/roadmap.md`
   - Ensure sprint aligns with quarterly goals
   - Identify any urgent items (user-reported bugs, deadlines)

## Planning (60 min)

4. **Define Sprint Goal**
   - One sentence: "What are we achieving this sprint?"
   - Example: "Launch subscription billing MVP"

5. **Select User Stories**
   - Choose 2-4 stories from backlog (based on goal + velocity)
   - Create story files in `active/stories/`
   - Fill in: Definition, Acceptance Criteria, Technical Approach

6. **Break Down into Tasks**
   - For each story, create 3-8 tasks
   - Each task: One agent, 2-6 hours, clear deliverable
   - Create task files in `active/tasks/`
   - Fill in: Requirements, Acceptance Criteria, Context

7. **Assign & Prioritize**
   - Assign tasks to agents (check their current workload)
   - Set priorities: P0 (blocker), P1 (high), P2 (medium)
   - Identify dependencies (Task B needs Task A complete first)

8. **Create Sprint.md**
   - List all stories and tasks
   - Include metadata (estimates, priorities, dependencies)
   - Set sprint dates (start, end)

## Launch (5 min)

9. **Announce Sprint**
   - Send message in chat.jsonl:
   ```json
   {
     "sender": "product-manager",
     "recipients": ["all"],
     "type": "info",
     "subject": "Sprint 3 Started",
     "message": "Sprint 3 (Feb 1-14) started. Goal: Complete subscription billing MVP. See sprint.md for tasks. Questions? Reply here.",
     "references": [],
     "priority": "normal"
   }
   ```

## During Sprint (Daily Check-In, 15 min)

10. **Monitor Progress**
    - Read sprint.md (check task statuses)
    - Read chat.jsonl (check for blockers)
    - Unblock agents (create tasks, make decisions)

11. **Reprioritize if Needed**
    - If major blocker: Shift priorities
    - If scope creep: Push items back to backlog
    - Update sprint.md

## End of Sprint (30 min)

12. **Review Completion**
    - Mark incomplete tasks (move to next sprint or backlog)
    - Archive completed stories
    - Extract lessons to lessons.md
    - Calculate velocity (for future planning)

13. **Retrospective** (optional)
    - What went well?
    - What could improve?
    - Add process improvements to lessons.md
```

**How PM Uses This**:
PM's prompt says: "For sprint planning, follow `.claude/workflows/sprint-planning.md`."

---

## 8. Agent-Specific Behaviors

### Product Manager

**Tools**: Read, Write, Glob, Grep, WebFetch, WebSearch  
**Model**: Sonnet  
**Primary Domain**: Task management, coordination

**Responsibilities**:
- Break user stories into tasks
- Maintain sprint.md and backlog.md
- Monitor progress, unblock agents
- Make decisions when agents disagree
- Archive completed sprints, extract lessons

**Session Start**:
1. Standard protocol (project.md, chat, sprint.md)
2. Read all agent status from sprint.md
3. Identify blockers and make plan

**Key Behaviors**:
- **Task Creation**: Clear acceptance criteria, one agent, one deliverable
- **Decision Making**: Quick decisions (< 1 day), document rationale
- **Don't Micromanage**: Trust agents on "how", focus on "what" and "when"
- **Unblocking**: Create tasks to unblock, don't wait for agents to fix themselves

**Workflows**:
- Sprint planning: Follow `.claude/workflows/sprint-planning.md`
- Weekly: Summarize chat, update lessons.md

---

### UI/UX Designer

**Tools**: Read, Write, Glob, Grep, WebFetch  
**Model**: Sonnet  
**Primary Domain**: Component specs, design system

**Responsibilities**:
- Create component specifications (not implementations)
- Maintain design system standards
- Audit frontend for consistency

**Key Behaviors**:
- **Spec Creation**: Write detailed specs in `design_docs/components/`
- **Format**: Include states, variants, responsive behavior, accessibility
- **Frontend Implements**: Designer creates blueprint, Frontend builds it
- **Can Request Changes**: If implementation doesn't match spec

**Session Start**:
1. Standard protocol
2. Check sprint for new UI tasks
3. Read recent frontend changes (audit for consistency)

**Example Spec** (reference for prompt):
```markdown
# Button Component Spec

## Variants
- **Primary**: Blue background, white text
- **Secondary**: White background, blue border, blue text
- **Danger**: Red background, white text

## Sizes
- **Small**: 32px height, 12px padding
- **Medium**: 40px height, 16px padding
- **Large**: 48px height, 20px padding

## States
- **Default**: Solid color
- **Hover**: Slightly darker
- **Disabled**: Grayed out, not clickable
- **Loading**: Show spinner, disabled

## Accessibility
- Use semantic <button> tag
- Include aria-label if icon-only
- Keyboard accessible (Tab, Enter, Space)
- Focus visible (outline on focus)

## Responsive
- Full width on mobile (<640px)
- Auto width on desktop

## Implementation Notes
- Use Tailwind classes
- Follow naming: `btn-{variant}-{size}`
```

---

### Frontend Developer

**Tools**: Read, Write, Edit, Bash, Glob, Grep  
**Model**: Sonnet  
**Primary Domain**: /src/client/**

**Responsibilities**:
- Implement UI from designer specs
- Client-side logic and state management
- API integration
- Write tests (unit + E2E)

**Session Start**:
1. Standard protocol
2. Check for new component specs (`design_docs/components/`)
3. Check for API contracts (if backend APIs ready)

**Key Behaviors**:
- **Read Specs First**: Always check `design_docs/components/` before building
- **Match Specs Exactly**: If deviation needed, document why
- **Cross-Domain**: Can fix backend bugs if blocking, document in commit
- **Escalate Bugs**: Complex bugs → send blocker message, tag Debugger

**Testing Requirements**:
- Unit tests for hooks, utilities
- E2E tests for critical flows (use Playwright)

---

### Backend Developer

**Tools**: Read, Write, Edit, Bash, Glob, Grep  
**Model**: Sonnet  
**Primary Domain**: /src/server/**

**Responsibilities**:
- Implement APIs
- Database design and migrations
- Business logic
- Write tests (unit + integration)

**Session Start**:
1. Standard protocol
2. Check database schema if making changes
3. Check if frontend needs APIs (coordinate)

**Key Behaviors**:
- **API Contracts**: Document all endpoints in task file
- **Database Ownership**: All schema decisions
- **Cross-Domain**: Can fix frontend bugs if blocking, document in commit
- **Escalate Bugs**: Complex bugs → send blocker message, tag Debugger

**Testing Requirements**:
- Unit tests for services, utilities
- Integration tests for all API endpoints

---

### Code Reviewer

**Tools**: Read, Grep, Glob, Bash, Edit (for minor fixes)  
**Model**: Opus  
**Primary Domain**: Quality assurance (all code)

**Responsibilities**:
- Review all code before merge
- Can make minor fixes (< 10 lines)
- Request changes for major issues
- Enforce conventions

**Session Start**:
1. Standard protocol
2. Check sprint.md for "Complete" tasks needing review
3. Skim lessons.md (recent entries for common issues)

**Key Behaviors**:
- **Read Checklist**: Always use `.claude/workflows/code-review-checklist.md`
- **Minor Fixes**: Typos, formatting, simple bugs → Fix directly and commit
- **Major Issues**: Architecture, security, logic → Request changes in story memory
- **Focus on Patterns**: Not nitpicks, look for systematic issues

**Decision Matrix** (when to fix vs. request changes):

| Issue Type | Severity | Action |
|------------|----------|--------|
| Typo in comment | Low | Fix directly |
| Missing error handling | Medium | Fix if simple (add try/catch), else request |
| Security vulnerability | High | Request changes + escalate to PM |
| Architecture problem | High | Request changes + document reasoning |
| Missing tests | Medium | Request changes |
| Code formatting | Low | Fix directly |

**Workflows**:
- Code review: Follow `.claude/workflows/code-review-checklist.md`

---

### Debugger

**Tools**: Read, Write, Edit, Bash, Glob, Grep  
**Model**: Opus  
**Primary Domain**: On-demand (any code)

**Responsibilities**:
- Deep investigation of complex bugs
- Root cause analysis
- Implement fixes with regression tests
- Write postmortems

**Session Start**:
1. Standard protocol
2. Check chat for blocker messages tagging you
3. Read lessons.md (check for similar past issues)

**Key Behaviors**:
- **Invoked by Blockers**: Don't self-assign, wait for agent to tag you
- **Root Cause Focus**: Don't just fix symptoms, find underlying issue
- **Regression Tests**: Always add test to prevent recurrence
- **Postmortems**: Write to lessons.md if relevant for future

**Debugging Protocol** (follow `.claude/workflows/debugging-protocol.md`):
```markdown
# Debugging Protocol

## Step 1: Reproduce (30 min max)
- Read task/story file for context
- Follow exact steps from blocker message
- Get it to fail consistently
- Document reproduction steps

## Step 2: Isolate (30 min)
- Narrow down where issue occurs (file, function, line)
- Add logging/breakpoints
- Test hypotheses (comment out code, add assertions)
- Identify variables/conditions that trigger bug

## Step 3: Root Cause (30 min)
- Why did this happen? (not just what)
- Check git history (when was this introduced?)
- Read related code (is this a pattern?)
- Check lessons.md (have we seen this before?)

## Step 4: Fix (30 min)
- Implement minimal fix (don't refactor everything)
- Add regression test (proves bug is fixed)
- Test edge cases
- Update code if needed

## Step 5: Document (15 min)
- Update task file with findings
- If significant learning, add to lessons.md:
  ```markdown
  ## [Category]
  **Symptom**: [What user saw]
  **Root Cause**: [Why it happened]
  **Solution**: [How to fix]
  **Prevention**: [How to avoid in future]
  ```
- Send resolution message in chat

## Example Postmortem (for lessons.md)
### Database Connection Pool Exhausted
**Symptom**: API requests timing out after 5 minutes of high load  
**Root Cause**: Prisma client not releasing connections after queries. Default pool size (10) exhausted.  
**Solution**: Increase pool size to 20 in Prisma config, add connection timeout (10s)  
**Prevention**: Monitor active connections in production. Add alert if > 80% pool used.
```

---

### DevOps Engineer

**Tools**: Read, Write, Edit, Bash, Glob, Grep  
**Model**: Sonnet  
**Primary Domain**: Deployment, infrastructure, CI/CD

**Responsibilities**:
- Set up and maintain CI/CD pipelines
- Manage deployments
- Infrastructure and environment configuration
- Secrets management

**Session Start**:
1. Standard protocol
2. Check for deployment requests
3. Monitor infrastructure health (if monitoring set up)

**Key Behaviors**:
- **Environment Ownership**: All .env, secrets, configs
- **Cross-Domain**: Can modify any config for infrastructure needs
- **Documentation**: Maintain runbooks in docs/

**Workflows**:
- Deployment: Follow `.claude/workflows/deployment-checklist.md`

---

## 9. System Prompt Architecture

### Overview

```
┌─────────────────────────────────────┐
│      CLAUDE.md (Shared Base)        │
│  • Philosophy & principles          │
│  • File structure overview          │
│  • Memory system (3 levels)         │
│  • Communication protocol           │
│  • Session start/during/end         │
│  • Code quality standards           │
└──────────────┬──────────────────────┘
               │ All agents inherit
               │
      ┌────────┴────────┬────────────────┐
      ▼                 ▼                ▼
 ┌─────────┐     ┌──────────┐    ┌──────────┐
 │ product-│     │ frontend-│    │ backend- │
 │ manager │     │ developer│    │ developer│
 │   .md   │     │   .md    │    │   .md    │
 └─────────┘     └──────────┘    └──────────┘
   + PM-specific  + FE patterns  + BE patterns
   workflows      + React rules  + API design
```

---

### CLAUDE.md (Shared Base Prompt)

**Location**: `.claude/CLAUDE.md`

**Contents** (prompt builder should create this):

1. **Introduction**
   - What this framework is
   - Philosophy (autonomy, minimal overhead, quality)
   - Your role in the team

2. **File Structure Overview**
   - Quick visual map
   - Where to find what

3. **Memory System**
   - 3 levels explained
   - When to update each level
   - How to load context progressively

4. **Communication Protocol**
   - When to communicate (blockers, decisions)
   - When NOT to communicate
   - How to read/write chat.jsonl
   - Message format examples

5. **Session Protocols**
   - Start checklist (embedded, not referenced)
   - During behaviors
   - End checklist (embedded)

6. **Code Quality Standards**
   - From project.md, summarized
   - Testing requirements
   - Documentation expectations

7. **Ownership & Cross-Domain**
   - Flexible ownership model
   - When/how to modify other domains
   - Documentation requirements

8. **Quality Gates**
   - Code review is mandatory
   - No self-merge rule
   - Testing before review

---

### Agent-Specific Prompts

**Location**: `.claude/agents/{role}.md`

**Format** (YAML frontmatter + Markdown):
```markdown
---
name: backend-developer
description: Expert in API development, database design, and server-side logic. Use for backend features, API endpoints, database migrations, and business logic implementation.
tools: Read, Write, Edit, Bash, Glob, Grep
model: sonnet
---

# Backend Developer

You are the Backend Developer responsible for server-side implementation.

## Your Domain
- **Primary**: /src/server/** (you own this)
- **Expertise**: APIs, databases, business logic
- **Deliverables**: Tested, documented backend features

## Session Start (Additional Steps)
After the shared protocol in CLAUDE.md:
1. Check if frontend needs APIs (check sprint.md, chat)
2. If making database changes, review current schema
3. Read backend-specific patterns in project.md

## Key Responsibilities
[Details from section 8]

## Decision Authority
[What you can decide autonomously]

## Communication Triggers
[When to send messages]

## Workflows
[References to workflow files you should read]

## Examples
[Concrete examples of good work]
```

**Each Agent Prompt Includes**:
1. Role definition
2. Primary domain
3. Session start additions (beyond shared protocol)
4. Responsibilities
5. Decision authority
6. Communication triggers
7. Workflows to follow
8. Examples of good outputs

---

## 10. Implementation Checklist

### Phase 1: Foundation (Week 1)

**Setup File Structure**:
- [ ] Create `.claude/` directory structure
- [ ] Create `design_docs/` with initial docs
- [ ] Create `design_docs/components/` for UI specs
- [ ] Set up `.gitignore` (ignore chat.jsonl, task files)

**Create Memory Files**:
- [ ] Write `.claude/memory/project.md` (see template in section 4)
- [ ] Create `.claude/memory/lessons.md` (empty initially)
- [ ] Create `.claude/memory/backlog.md` (seed with first items)

**Create Base Prompts**:
- [ ] Write `.claude/CLAUDE.md` (shared base prompt)
- [ ] Create `.claude/agents/product-manager.md`
- [ ] Create `.claude/agents/frontend-developer.md`
- [ ] Create `.claude/agents/backend-developer.md`
- [ ] Create `.claude/agents/code-reviewer.md`

**Create Workflows**:
- [ ] Write `.claude/workflows/code-review-checklist.md`
- [ ] Write `.claude/workflows/sprint-planning.md`
- [ ] Write `.claude/workflows/debugging-protocol.md`

**Test with Simple Story**:
- [ ] PM creates first sprint.md
- [ ] PM creates simple user story (e.g., "Hello World" API)
- [ ] Backend implements
- [ ] Code Reviewer reviews
- [ ] Observe: Does communication work? Is context sufficient?

---

### Phase 2: Refinement (Week 2)

**Add Remaining Agents**:
- [ ] Create `.claude/agents/ui-ux-designer.md`
- [ ] Create `.claude/agents/debugger.md`
- [ ] Create `.claude/agents/devops-engineer.md`

**Refine Based on Phase 1**:
- [ ] Update CLAUDE.md based on issues encountered
- [ ] Add to lessons.md (what went wrong, how to fix)
- [ ] Adjust workflows if needed

**Test Complex Story**:
- [ ] Story requiring FE + BE + Designer coordination
- [ ] Intentionally introduce bug to test Debugger
- [ ] Verify chat.jsonl filtering works

---

### Phase 3: Production (Week 3+)

**Optimize**:
- [ ] Set up chat summarization (PM weekly task)
- [ ] Archive first sprint
- [ ] Measure: Are agents spending 90% time coding?

**Scale**:
- [ ] Add business agents (Growth, Customer Success) post-MVP
- [ ] Refine prompts based on real usage

---

## 11. Quick Reference

### File Locations Cheat Sheet

```
Project Overview:        .claude/memory/project.md
Past Mistakes:           .claude/memory/lessons.md
Future Work:             .claude/memory/backlog.md
Current Sprint:          .claude/active/sprint.md
User Stories:            .claude/active/stories/US###.md
Tasks:                   .claude/active/tasks/T###.md
Group Chat:              .claude/communication/chat.jsonl
Component Specs:         design_docs/components/*.md
Workflows:               .claude/workflows/*.md
```

---

### Session Protocol Quick Reference

```
START (5 min):
  Load project.md → Check chat → Read sprint → Pick task → Load task file

WORK:
  Update task memory → Communicate if blocked/decision → Progressive loading

END (5 min):
  Commit → Summarize to story → Update sprint → Clean up
```

---

### Communication Quick Reference

**Send Message When**:
- ❌ Blocked by another agent
- ⚠️ Need important decision

**Don't Message For**:
- ✅ Implementation details (you decide)
- ✅ Status updates (use sprint.md)
- ✅ Questions answerable by code

---

### Memory Lifecycle

```
Task Memory (session) 
    → Summarized to Story Memory 
    → Story Complete 
    → Lessons Extracted to lessons.md 
    → Story Archived
```

---

## Appendix: Decision Log

### Design Decisions for This Framework

**D1: Why JSONL for Chat?**
- **Decision**: Use JSONL, not Markdown
- **Why**: Agents can filter without reading entire file (parse line by line)
- **Alternative**: Markdown with structured format (harder to filter)
- **Trade-off**: Slightly more complex to parse, but worth it for efficiency

**D2: Why Combine Task File + Task Memory?**
- **Decision**: Task context and task memory in one file
- **Why**: Agent reads ONE file to get everything
- **Alternative**: Separate files (more jumping around)
- **Trade-off**: File can get long, but progressive sections help

**D3: Why Delete Task Files When Done?**
- **Decision**: Delete after summarizing to story
- **Why**: Active directory stays small, context preserved in story
- **Alternative**: Archive task files (more clutter, harder to find relevant context)
- **Trade-off**: Lose detailed implementation notes, but summary captures key points

**D4: Why Allow Code Reviewer to Make Minor Fixes?**
- **Decision**: Code Reviewer can fix small issues directly
- **Why**: Avoid ping-pong for typos/formatting
- **Alternative**: Always request changes (slower, more overhead)
- **Trade-off**: Need clear definition of "minor" (< 10 lines, no logic changes)

**D5: Why 3-Level Memory vs. More Levels?**
- **Decision**: Project → Story → Task (no more granular)
- **Why**: Enough separation without overwhelming
- **Alternative**: Add "module level" or "file level" (too much overhead)
- **Trade-off**: Some duplication between levels, but acceptable

---

## Conclusion

This framework enables autonomous multi-agent SaaS development with:

- **90% time coding, 10% coordination**
- **Minimal communication** (only blockers + decisions)
- **One file per context unit** (easy loading)
- **Progressive context** (read what you need)
- **Trust + verify** (autonomy + mandatory review)

**Next Steps**:
1. Pass this document to prompt-writing agent
2. Agent creates: CLAUDE.md, all agent/*.md files, workflow files
3. Test with simple user story
4. Iterate based on real behavior

**Success Metrics**:
- Agents know what to work on (< 5% blocked by unclear direction)
- Context loads in < 5 minutes per session
- Code review catches issues (< 5% post-merge bugs)
- Communication stays light (< 5 messages per day per agent)

---

**Document Version**: 1.0  
**Last Updated**: 2025-01-XX  
**Maintained By**: Product Manager Agent
