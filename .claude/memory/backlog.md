# Backlog

Structured queue of ready-to-work tickets. Not prioritized or assigned yet. PM pulls items from here into sprints during sprint planning.

---

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
**References**: design_docs/user_stories.md (if exists)

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
**References**: design_docs/user_stories.md (if exists)

### F003: User Profile Page
**Priority**: P1 (High)
**Estimate**: 6 hours
**Description**: Allow users to view and edit their profile information
**Acceptance Criteria**:
- Display current user info (name, email, avatar)
- Allow editing name and avatar
- Email change requires verification
- Show account creation date
- Responsive design
**Technical Notes**:
- Frontend: Create ProfilePage component
- Backend: PATCH /api/v1/users/:id endpoint
- Use React Hook Form for validation
- Image upload for avatar (local storage or S3?)
**Dependencies**: Authentication system must exist
**References**: design_docs/components/profile-page.md (Designer creates)

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
**References**: Reported by user, Issue #45 (if using GitHub issues)

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
- May need polyfill for Canvas API
**References**: Reported by 3 users, Issue #52

### B003: Email Notifications Not Sending
**Priority**: P0 (Critical)
**Estimate**: 1 hour
**Description**: Users not receiving email notifications
**Steps to Reproduce**:
1. Sign up for new account
2. Check email for welcome message
3. No email received
**Technical Notes**:
- Check email service logs
- Verify SMTP credentials in .env
- Check spam folder
- May need to configure SPF/DKIM records
**References**: Reported by multiple users

---

## Technical Debt

### TD001: Refactor Auth Service (Too Many Responsibilities)
**Priority**: P3 (Low)
**Estimate**: 4 hours
**Description**: auth.ts service has 500+ lines, doing too much
**Why**: Hard to test, hard to understand, violates Single Responsibility Principle
**Proposed Solution**:
- Split into:
  - auth.ts (orchestration)
  - password.ts (hashing/validation)
  - jwt.ts (token logic)
  - user.ts (user CRUD)
- Keep public interface same (no breaking changes)
**Benefit**: Better testability, easier to maintain
**Risk**: Low (refactor only, no behavior change)
**Files Affected**: src/server/services/auth.ts

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
**Risk**: Low (indexes don't change behavior, only performance)
**References**: Performance monitoring data

### TD003: Extract Reusable Form Components
**Priority**: P3 (Low)
**Estimate**: 5 hours
**Description**: Form code duplicated across login, signup, profile pages
**Why**: Violates DRY principle, makes changes harder
**Proposed Solution**:
- Create reusable components: FormInput, FormSelect, FormCheckbox
- Use React Hook Form + Zod for all forms
- Extract common validation schemas
**Benefit**: Less code duplication, consistent UX, easier to maintain
**Risk**: Low (can migrate forms incrementally)
**Files Affected**: src/client/components/features/*

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
**Risk**: Medium (need to handle cache invalidation on user updates)
**References**: Performance monitoring shows 1000+ user queries/minute

### O002: Lazy Load Dashboard Components
**Priority**: P2 (Medium)
**Estimate**: 3 hours
**Description**: Dashboard loads all widgets upfront (slow initial load)
**Current**: 5 widgets loaded synchronously (bundle size: 800KB)
**Proposed**: React.lazy() for below-fold widgets
**Benefit**: Faster initial render (bundle: 800KB → 300KB initial)
**Risk**: Low (React.lazy is stable)
**References**: Lighthouse score: 65 → expected 85+

### O003: Implement API Response Compression
**Priority**: P3 (Low)
**Estimate**: 1 hour
**Description**: API responses not compressed, wasting bandwidth
**Current**: JSON responses sent uncompressed
**Proposed**: Add compression middleware (gzip)
**Benefit**: Reduce response size by ~70%, faster for users on slow connections
**Risk**: Low (standard practice)
**References**: Network tab shows 500KB responses

---

## Documentation

### D001: Create API Documentation
**Priority**: P2 (Medium)
**Estimate**: 4 hours
**Description**: No central API documentation for frontend developers
**Proposed**:
- Create docs/api/ with endpoint documentation
- Include request/response examples
- Document error codes
- Use OpenAPI/Swagger format
**Benefit**: Easier frontend integration, less back-and-forth
**Dependencies**: None

### D002: Add Onboarding Guide
**Priority**: P3 (Low)
**Estimate**: 3 hours
**Description**: New developers struggle to set up project
**Proposed**:
- Create docs/guides/getting-started.md
- Step-by-step setup instructions
- Common issues and solutions
- Links to key files
**Benefit**: Faster onboarding for new team members
**Dependencies**: None

---

## Format Guidelines

### When Adding Items

**Features**:
- Must have clear acceptance criteria
- Should reference design docs
- Estimate in hours
- Note dependencies

**Bugs**:
- Include reproduction steps
- Expected vs actual behavior
- Browser/environment if relevant
- Severity: P0 (critical), P1 (high), P2 (medium), P3 (low)

**Technical Debt**:
- Explain why it's debt
- Propose solution
- Estimate effort and risk
- List affected files

**Optimizations**:
- Show current vs proposed metrics
- Estimate benefit and cost
- Note risks
- Include data/references

### Priority Guidelines

- **P0 (Critical)**: Blocking users, security issue, data loss
- **P1 (High)**: Important feature or bug affecting many users
- **P2 (Medium)**: Nice to have, affects some users
- **P3 (Low)**: Polish, optimization, technical debt

### Estimate Guidelines

- **Small (1-3 hours)**: Single file changes, simple features
- **Medium (4-6 hours)**: Multi-file changes, moderate complexity
- **Large (7-10 hours)**: Multiple components, requires design/planning
- **X-Large (10+ hours)**: Should be broken into multiple tasks

---

**Management**:
- Any agent can add items (must follow format)
- PM reviews weekly, pulls into sprints
- PM updates priorities based on user feedback, urgency
- Keep backlog under 50 items (archive low-priority items after 3 months)
