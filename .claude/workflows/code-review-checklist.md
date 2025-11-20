# Code Review Checklist

Use this checklist for every code review. Check each applicable item systematically.

## Functionality

- [ ] **Meets Acceptance Criteria**: All requirements from story/task file satisfied
- [ ] **Correct Implementation**: Logic implements what was specified, not something else
- [ ] **Expected Inputs**: Handles expected inputs correctly
- [ ] **Edge Cases**: Handles edge cases (empty arrays, null values, invalid input, boundary conditions)
- [ ] **Error Messages**: Clear, actionable, user-friendly

## Code Quality

- [ ] **Function Size**: Functions <100 lines (split if larger)
- [ ] **Single Responsibility**: Each function does one thing
- [ ] **DRY Principle**: No code duplication (repeated logic extracted to shared function)
- [ ] **Naming**: Descriptive variable/function/class names (no abbreviations, no single letters except loop counters)
- [ ] **Comments**: Complex logic has comments explaining "why" (not "what")
- [ ] **Readability**: Code is self-documenting, easy to understand
- [ ] **No Dead Code**: No commented-out code, unused imports, unreachable code
- [ ] **Consistent Style**: Follows project conventions (see project.md)

## Testing

- [ ] **Unit Tests**: Business logic in services has unit tests (aim 80% coverage)
- [ ] **Integration Tests**: API endpoints have integration tests (request → response)
- [ ] **E2E Tests**: Critical user flows have E2E tests (if applicable)
- [ ] **All Tests Passing**: No failing tests (check CI/CD)
- [ ] **Edge Case Coverage**: Tests cover edge cases, not just happy path
- [ ] **Regression Tests**: Bug fixes include test to prevent recurrence
- [ ] **Test Quality**: Tests are clear, focused, don't test implementation details

## Security (OWASP Top 10)

- [ ] **SQL Injection**: No string concatenation in queries (use parameterized queries, ORMs)
- [ ] **XSS Prevention**: User input sanitized, outputs escaped
- [ ] **Authentication**: Protected endpoints require authentication
- [ ] **Authorization**: Users can only access their own resources (ownership checks)
- [ ] **Secrets Management**: No hardcoded secrets (API keys, passwords, tokens in .env)
- [ ] **Logging**: Sensitive data not logged (passwords, tokens, credit cards, PII)
- [ ] **Rate Limiting**: Auth, payment, email endpoints have rate limiting
- [ ] **HTTPS Only**: Production uses HTTPS (check deployment config)
- [ ] **CORS**: CORS configured correctly (not allowing all origins in production)
- [ ] **Dependencies**: No known vulnerabilities in dependencies (check npm audit)

## Performance

- [ ] **N+1 Queries**: No queries in loops (use eager loading, joins)
- [ ] **Database Indexes**: Frequently queried fields have indexes
- [ ] **Unnecessary API Calls**: No redundant or excessive API requests
- [ ] **Pagination**: Large lists paginated (not loading 1000+ items at once)
- [ ] **Caching**: Appropriate caching (React Query staleTime, server-side caching)
- [ ] **Image Optimization**: Images optimized (compressed, lazy-loaded, appropriate format)
- [ ] **Bundle Size**: Frontend bundles reasonable size (check webpack bundle analyzer)
- [ ] **Memoization**: Expensive computations memoized where appropriate

## Frontend-Specific

- [ ] **Responsive Design**: Works on mobile (320px+), tablet, desktop
- [ ] **Cross-Browser**: Works on Chrome, Firefox, Safari, Edge
- [ ] **All States**: Implements loading, error, success, empty states
- [ ] **Matches Spec**: Follows Designer's component spec (if exists)
- [ ] **Design System**: Consistent with design system (colors, typography, spacing)
- [ ] **Accessibility**:
  - [ ] Keyboard navigable (Tab, Enter, Esc work)
  - [ ] Screen reader friendly (semantic HTML, ARIA labels)
  - [ ] Focus indicators visible
  - [ ] Color contrast sufficient (WCAG AA: 4.5:1 for text)
  - [ ] Forms have labels
  - [ ] Error messages associated with fields (aria-describedby)
- [ ] **Error Boundaries**: Components wrapped in error boundaries
- [ ] **Loading States**: Async operations show loading state
- [ ] **Empty States**: Empty data shows helpful message (not blank screen)
- [ ] **Form Validation**: Client-side validation with clear error messages
- [ ] **No Console Errors**: No errors or warnings in browser console

## Backend-Specific

- [ ] **RESTful Conventions**: Follows REST principles (proper HTTP methods, routes)
- [ ] **Status Codes**: Correct HTTP status codes (200, 201, 400, 401, 404, 500, etc.)
- [ ] **Input Validation**: All inputs validated (Zod schemas, not just TypeScript types)
- [ ] **Error Handling**: Comprehensive error handling (try/catch, error middleware)
- [ ] **Error Format**: Consistent error response format across all endpoints
- [ ] **API Documentation**: Endpoints documented (in task file or docs/)
- [ ] **Database Transactions**: Multi-step database operations use transactions
- [ ] **Migrations**: Database changes have migration files
- [ ] **Idempotency**: POST/PUT operations idempotent where appropriate
- [ ] **Request Logging**: Requests logged (for debugging, monitoring)

## Architecture & Patterns

- [ ] **Follows Project Patterns**: Adheres to established patterns (see project.md)
- [ ] **Separation of Concerns**: Clear boundaries between layers (controller/service/repository)
- [ ] **SOLID Principles**: Follows SOLID, especially Single Responsibility
- [ ] **Appropriate Abstractions**: Not over-engineered, not under-abstracted
- [ ] **No Tight Coupling**: Components/modules loosely coupled
- [ ] **Dependency Injection**: Dependencies injected, not hardcoded
- [ ] **Error Propagation**: Errors handled at appropriate layer

## Documentation

- [ ] **Function Docstrings**: Complex functions have docstrings (purpose, params, returns)
- [ ] **API Documentation**: New endpoints documented (request/response examples)
- [ ] **README Updated**: Setup instructions updated if process changed
- [ ] **Comments**: Complex logic explained in comments
- [ ] **Lessons Extracted**: Significant learnings added to lessons.md
- [ ] **Architecture Decisions**: Major decisions documented (in story or project.md)
- [ ] **Types/Interfaces**: TypeScript types/interfaces documented if non-obvious

## File Organization & Conventions

- [ ] **File Size**: Files <300 lines (split if larger)
- [ ] **File Location**: Files in correct directory (see project.md structure)
- [ ] **Naming Conventions**: Files named correctly (kebab-case, PascalCase as appropriate)
- [ ] **Import Order**: Imports organized (external, internal, types, styles)
- [ ] **Exports**: Appropriate exports (named vs default)
- [ ] **No Circular Dependencies**: No circular imports

## Cross-Domain Changes

- [ ] **Justified**: Cross-domain changes have clear justification
- [ ] **Documented**: Reason explained in commit message
- [ ] **Necessary**: Change necessary, not just convenient
- [ ] **Coordinated**: Domain owner aware of change (if significant)

## Git & Version Control

- [ ] **Commit Messages**: Clear, descriptive commit messages (includes task ID)
- [ ] **Atomic Commits**: Each commit is logical unit of work
- [ ] **No Merge Conflicts**: Conflicts resolved correctly
- [ ] **Branch Strategy**: Follows branching strategy
- [ ] **No Secrets Committed**: No .env files, API keys, passwords in commits

## Review Decision

After checking all applicable items:

### Approve ✓
Use when:
- All critical items checked
- Any issues found are minor (can fix directly)
- Code meets acceptance criteria
- Tests passing
- Ready to merge

**Actions**:
- Fix minor issues (<10 lines each)
- Document fixes in review notes
- Update story: "Code Review: Approved ✓"
- Update sprint.md: "✅ Complete"

### Request Changes 🔄
Use when:
- Major issues found (architecture, security, logic errors)
- Missing tests
- Doesn't meet acceptance criteria
- Tests failing

**Actions**:
- Document all issues in story file under "Code Review" section
- For each issue: severity, location, description, recommendation
- Update sprint.md: "🔄 Review - Changes Requested"
- Send message to implementer with specifics
- Estimate time to fix

---

## Notes for Reviewers

**Be Thorough**: Don't skip items. Quality gate is critical.

**Be Constructive**: Frame issues as problems to solve, suggest solutions.

**Be Consistent**: Apply same standards to all code.

**Be Timely**: Review within 1 day of request (don't block team).

**Fix vs. Request**:
- Fix: Typos, formatting, simple bugs (<10 lines)
- Request: Architecture, security, logic errors, missing tests

**Security First**: Never approve code with security vulnerabilities.

**Tests Must Pass**: Don't review until tests pass.

---

## Quick Decision Matrix

| Issue Type | Action |
|------------|--------|
| Typo in comment | Fix directly |
| Missing error handling | Fix if simple, else request |
| Security vulnerability | Request changes + escalate |
| Architecture problem | Request changes + explain |
| Missing tests | Request changes |
| Code formatting | Fix directly |
| N+1 query | Request changes |
| Logic error | Fix if <10 lines, else request |
| Doesn't meet acceptance criteria | Request changes |
| No documentation | Request changes if significant feature |

---

**Version**: 1.0
**Last Updated**: 2025-01-20
**Maintained By**: Code Reviewer
