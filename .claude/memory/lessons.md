# Lessons Learned

This file contains curated learnings from past mistakes, bugs, and discoveries. Used to prevent repeating errors and share knowledge across agent sessions.

## Format
Each lesson should include:
- **Category**: (Authentication, Database, Frontend, Backend, Performance, Security, etc.)
- **Symptom**: What the user/developer saw
- **Root Cause**: Why it happened
- **Solution**: How it was fixed
- **Prevention**: How to avoid in the future

---

## Authentication

### JWT Secret Must Be In Environment Variables
**Symptom**: JWT tokens not validating correctly across restarts
**Root Cause**: JWT secret was hardcoded and changed on each deployment
**Solution**: Move JWT_SECRET to .env file, use same secret across deployments
**Prevention**: Never hardcode secrets. Always use environment variables. Add to .env.example as template.

### Always Validate Inputs on Backend
**Symptom**: Invalid data getting into database despite frontend validation
**Root Cause**: Relied solely on frontend validation, users bypassed with direct API calls
**Solution**: Implement Zod validation schemas on all API endpoints
**Prevention**: Frontend validation is for UX. Backend validation is for security. Always validate at API boundary.

---

## Database

### Prisma Unique Constraint Errors Need Explicit Handling
**Symptom**: Server crashes on duplicate email instead of returning proper error
**Root Cause**: Prisma throws PrismaClientKnownRequestError with code 'P2002' but wasn't caught
**Solution**: Wrap create operations in try/catch, check error.code === 'P2002'
**Prevention**: Always handle Prisma-specific errors explicitly. Reference: https://www.prisma.io/docs/reference/api-reference/error-reference

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

### N+1 Query Problem in List Endpoints
**Symptom**: Dashboard endpoint taking 5+ seconds with 100 users
**Root Cause**: Loading users in loop, then fetching subscriptions for each (N+1 queries)
**Solution**: Use Prisma's `include` to eager load related data in single query
**Prevention**: Always use eager loading for related data. Monitor query counts in development.

---

## Frontend

### httpOnly Cookies Require CORS Credentials
**Symptom**: CORS errors when calling backend API from frontend
**Root Cause**: httpOnly cookies need `credentials: 'include'` in fetch and CORS middleware config
**Solution**:
- Frontend: `fetch(url, { credentials: 'include' })`
- Backend: `cors({ origin: 'http://localhost:5173', credentials: true })`
**Prevention**: When using httpOnly cookies for auth, set up CORS credentials from day 1.

### React Query Stale Time vs Cache Time
**Symptom**: API being called too frequently, wasting requests
**Root Cause**: Misunderstanding React Query's staleTime (when data is considered stale) vs cacheTime (how long to keep in cache)
**Solution**: Set appropriate staleTime for data that doesn't change often (e.g., user profile: 5 minutes)
**Prevention**: Read React Query docs on caching. Default staleTime is 0 (immediately stale).

---

## Backend

### Rate Limiting Must Be Before Auth
**Symptom**: Attackers can hammer login endpoint, bypassing rate limits if they fail auth
**Root Cause**: Rate limiting middleware placed after auth check
**Solution**: Move rate limiting middleware before auth in middleware chain
**Prevention**: Rate limiting should protect endpoints from abuse, regardless of auth status.

### Password Validation Edge Cases
**Symptom**: Empty string passing password regex validation
**Root Cause**: Regex didn't enforce minimum length, only pattern
**Solution**: Use Zod's `.min(8)` before `.regex()` to catch length violations
**Prevention**: Combine multiple validation rules. Don't rely on regex alone for complex validation.

---

## Performance

### Image Optimization for Web
**Symptom**: Homepage loading slowly, large bundle size
**Root Cause**: Using raw PNG images without optimization
**Solution**: Use next/image or similar with automatic WebP conversion and lazy loading
**Prevention**: Always optimize images. Use WebP format. Implement lazy loading for below-fold images.

---

## Security

### Never Log Sensitive Data
**Symptom**: Passwords and tokens appearing in logs
**Root Cause**: Logging request bodies without sanitization
**Solution**: Create logging middleware that filters sensitive fields (password, token, credit_card)
**Prevention**: Implement structured logging with automatic PII filtering from day 1.

```typescript
const SENSITIVE_FIELDS = ['password', 'token', 'credit_card', 'ssn'];
const sanitize = (obj) => {
  const clean = { ...obj };
  SENSITIVE_FIELDS.forEach(field => {
    if (clean[field]) clean[field] = '[REDACTED]';
  });
  return clean;
};
```

---

## Testing

### E2E Tests Must Clean Up Database
**Symptom**: E2E tests failing intermittently, "email already exists" errors
**Root Cause**: Test data not cleaned up between runs
**Solution**: Implement `beforeEach` and `afterEach` hooks to reset test database state
**Prevention**: E2E tests should be idempotent. Always clean up test data.

---

## DevOps

### Environment Variables in CI/CD
**Symptom**: Build failing in CI but working locally
**Root Cause**: Environment variables not set in CI environment
**Solution**: Add all required env vars to CI/CD secrets, reference in workflow
**Prevention**: Maintain .env.example with all required variables. Document in README.

---

## General Patterns

### Error Boundaries Save User Experience
**Symptom**: White screen of death when component crashes
**Root Cause**: No error boundary to catch React errors
**Solution**: Wrap app and major features in error boundaries with fallback UI
**Prevention**: Implement error boundaries early. Show user-friendly error messages, log details for debugging.

### API Versioning From Start
**Symptom**: Breaking changes forcing all clients to update
**Root Cause**: No API versioning, all endpoints at /api/{resource}
**Solution**: Use /api/v1/{resource} from day 1, maintain v1 while building v2
**Prevention**: Always version APIs. Makes backwards compatibility manageable.

---

**Note**: This file should be curated, not comprehensive. Only add lessons that:
1. Would prevent future bugs
2. Represent non-obvious patterns
3. Save significant debugging time
4. Apply broadly across the project

Maximum size: 1000 lines. When exceeding, archive old lessons and keep most relevant.
