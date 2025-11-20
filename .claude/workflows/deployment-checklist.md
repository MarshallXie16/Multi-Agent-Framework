# Deployment Checklist

Follow this checklist for every production deployment to ensure safe, reliable releases.

**Time Required**: 30-60 minutes (including monitoring)

---

## Pre-Deployment (15-20 min)

### 1. Verify Code Quality

- [ ] **All Tests Passing**:
  ```bash
  npm test
  ```
  - Unit tests ✓
  - Integration tests ✓
  - E2E tests ✓ (if applicable)
  - No failing tests
  - No skipped tests

- [ ] **Linting Clean**:
  ```bash
  npm run lint
  ```
  - No linting errors
  - No linting warnings (or documented exceptions)

- [ ] **Type Checking Passing**:
  ```bash
  npm run typecheck
  ```
  - No TypeScript errors

- [ ] **Build Successful**:
  ```bash
  npm run build
  ```
  - Frontend builds ✓
  - Backend builds ✓
  - No build errors

### 2. Code Review

- [ ] **All PRs Reviewed**:
  - Code reviewed and approved by Code Reviewer
  - All comments addressed
  - Changes requested resolved

- [ ] **Merged to Main**:
  - All feature branches merged
  - No pending commits
  - Main branch up to date

### 3. Environment Configuration

- [ ] **Environment Variables Set**:
  - Check `.env.example` for required vars
  - Verify all required vars set in production environment
  - Secrets configured (Stripe keys, JWT secret, etc.)
  - Database URL correct
  - API URLs correct

- [ ] **Check CI/CD Secrets**:
  - GitHub Actions secrets set
  - Vercel environment variables set
  - Railway environment variables set
  - No hardcoded secrets in code

### 4. Database Preparation

- [ ] **Review Migrations**:
  ```bash
  # Check migration files
  ls prisma/migrations/
  cat prisma/migrations/latest/migration.sql
  ```
  - Understand what migrations will run
  - No destructive changes (unless intended)
  - No data loss

- [ ] **Test Migrations Locally**:
  ```bash
  # Reset local database
  npx prisma migrate reset

  # Run migrations
  npx prisma migrate deploy
  ```
  - Migrations run successfully
  - No errors
  - Data intact

- [ ] **Backup Production Database**:
  ```bash
  # PostgreSQL backup
  pg_dump $DATABASE_URL > backup_$(date +%Y%m%d_%H%M%S).sql

  # Or use platform backup (Railway, Heroku)
  # Verify backup exists and is recent
  ```
  - Backup complete
  - Backup file size reasonable (not empty)
  - Backup stored safely

### 5. Communication

- [ ] **Announce Deployment**:
  ```json
  {
    "sender": "devops-engineer",
    "recipients": ["all"],
    "type": "info",
    "subject": "Production Deployment Starting - v1.2.0",
    "message": "Starting production deployment of v1.2.0. Includes: [list features/fixes]. Expected downtime: [X minutes if any]. ETA: [time].",
    "references": [],
    "priority": "normal"
  }
  ```

- [ ] **Check Team Availability**:
  - At least one team member available for next 30 minutes
  - Ready to respond if issues arise

### 6. Deployment Timing

- [ ] **Good Time to Deploy**:
  - Low traffic period (avoid peak hours)
  - Not Friday evening (if issue, hard to fix over weekend)
  - Team available to monitor
  - No major events/deadlines immediately after

---

## Deployment (10-15 min)

### 7. Deploy Backend

- [ ] **Deploy Backend First** (API compatibility):
  ```bash
  # If using Railway
  railway up --service backend

  # If using CI/CD, push to main triggers deploy
  git push origin main
  ```

- [ ] **Monitor Deployment**:
  - Watch deployment logs
  - Check for errors
  - Verify deployment successful

- [ ] **Check Health Endpoint**:
  ```bash
  curl https://api.example.com/health
  # Should return {"status":"healthy"}
  ```

### 8. Run Database Migrations

- [ ] **Run Migrations**:
  ```bash
  # Railway
  railway run npx prisma migrate deploy

  # Or in CI/CD workflow
  # (automatically runs after backend deploy)
  ```

- [ ] **Verify Migrations**:
  - Check migration output
  - No errors
  - All migrations applied

- [ ] **Check Database State**:
  ```bash
  # Connect to database
  railway run npx prisma studio
  # Or use database client (pgAdmin, DataGrip)

  # Verify:
  # - Tables exist
  # - Data intact
  # - Indexes created
  # - Constraints correct
  ```

### 9. Deploy Frontend

- [ ] **Deploy Frontend**:
  ```bash
  # If using Vercel, push to main triggers deploy
  git push origin main

  # Or manually
  vercel --prod
  ```

- [ ] **Monitor Deployment**:
  - Watch deployment logs
  - Check for errors
  - Verify deployment successful

- [ ] **Check Build Output**:
  - Build size reasonable (not bloated)
  - No missing assets
  - Source maps generated (for debugging)

---

## Post-Deployment Verification (10-15 min)

### 10. Smoke Test

- [ ] **Visit Production Site**:
  - https://app.example.com
  - Loads without errors

- [ ] **Test Critical Flows**:
  - [ ] Homepage loads
  - [ ] Can navigate to main pages
  - [ ] Can log in (test user)
  - [ ] Can log out
  - [ ] Critical features work (test each)
  - [ ] No JavaScript errors in console
  - [ ] No broken images/links

- [ ] **Test New Features**:
  - Test each feature in deployment
  - Verify works as expected
  - Check edge cases

### 11. Monitor Logs

- [ ] **Check Error Logs** (10 minutes):
  ```bash
  # Railway
  railway logs --service backend

  # Or error tracking (Sentry)
  # Check dashboard for new errors
  ```

- [ ] **Look For**:
  - No new errors
  - No spikes in error rate
  - No failing database queries
  - No authentication issues

### 12. Monitor Metrics

- [ ] **Check Performance**:
  - Response times normal
  - Database queries fast (<100ms)
  - No timeouts
  - No connection pool exhaustion

- [ ] **Check User Activity**:
  - Users able to access site
  - No increase in support requests
  - No complaints in user channels

### 13. Verify Database

- [ ] **Check Data Integrity**:
  - No data loss
  - Migrations applied correctly
  - Constraints enforced
  - Indexes working

- [ ] **Run Sample Queries**:
  ```sql
  -- Check user count (should not decrease)
  SELECT COUNT(*) FROM users;

  -- Check recent activity
  SELECT * FROM users ORDER BY created_at DESC LIMIT 10;
  ```

---

## Post-Deployment (5-10 min)

### 14. Document Deployment

- [ ] **Update Deployment Log**:
  ```markdown
  # Deployment Log

  ## v1.2.0 - 2024-01-20 14:30 UTC

  **Deployed By**: DevOps Engineer
  **Duration**: 15 minutes
  **Downtime**: None

  **Included**:
  - US002: Subscription billing (Stripe integration)
  - US003: User dashboard
  - Bug fix: Dashboard infinite loop

  **Database Migrations**:
  - Added subscriptions table
  - Added billing_info to users

  **Issues**: None

  **Rollback**: Not needed
  ```

- [ ] **Update Version** (if using versioning):
  ```bash
  npm version patch
  git push --tags
  ```

### 15. Announce Completion

- [ ] **Send Success Message**:
  ```json
  {
    "sender": "devops-engineer",
    "recipients": ["all"],
    "type": "info",
    "subject": "Production Deployment Complete - v1.2.0",
    "message": "v1.2.0 deployed successfully to production. All smoke tests passing. Monitoring for issues. Production URL: https://app.example.com",
    "references": [],
    "priority": "normal"
  }
  ```

### 16. Continue Monitoring

- [ ] **Monitor for 30-60 Minutes**:
  - Check logs periodically
  - Watch for error spikes
  - Monitor user reports
  - Be ready to respond to issues

- [ ] **Check Metrics**:
  - Error rate normal
  - Response times normal
  - User activity normal

---

## Rollback Plan (If Issues Found)

**Trigger Rollback If**:
- Critical functionality broken
- Data loss detected
- Security vulnerability
- Widespread user impact
- Error rate spike >10x normal

### Rollback Steps

#### 1. Stop the Bleeding

- [ ] **Announce Issue**:
  ```json
  {
    "sender": "devops-engineer",
    "recipients": ["all", "product-manager"],
    "type": "info",
    "subject": "CRITICAL: Rolling Back v1.2.0",
    "message": "Critical issue detected: [description]. Rolling back to v1.1.0. ETA: 10 minutes.",
    "references": [],
    "priority": "critical"
  }
  ```

#### 2. Revert Code

- [ ] **Revert Commit**:
  ```bash
  # Find commit to revert
  git log --oneline

  # Revert
  git revert <commit-hash>

  # Or reset to previous version
  git reset --hard <previous-commit>

  # Push
  git push origin main --force
  ```

- [ ] **Redeploy**:
  - CI/CD will auto-deploy revert
  - Or manually deploy previous version

#### 3. Rollback Database (If Needed)

- [ ] **Assess Database State**:
  - Were migrations destructive?
  - Is data loss possible?
  - Can migrations be reversed?

- [ ] **Restore Backup** (if necessary):
  ```bash
  # Stop application (prevent writes)
  railway down

  # Restore backup
  psql $DATABASE_URL < backup_YYYYMMDD_HHMMSS.sql

  # Restart application
  railway up
  ```

- [ ] **Or Rollback Migration** (if possible):
  ```bash
  # Prisma doesn't support migration rollback
  # Must restore backup or manually revert

  # Manual revert (if safe)
  psql $DATABASE_URL < revert_migration.sql
  ```

#### 4. Verify Rollback

- [ ] **Test Critical Flows**:
  - Site accessible
  - Users can log in
  - Critical features work
  - Data intact

- [ ] **Monitor Logs**:
  - Errors resolved
  - System stable

#### 5. Post-Rollback

- [ ] **Announce Complete**:
  ```json
  {
    "sender": "devops-engineer",
    "recipients": ["all"],
    "type": "info",
    "subject": "Rollback Complete - v1.1.0 Restored",
    "message": "Rolled back to v1.1.0. System stable. Investigating issue from v1.2.0. Will deploy fix after root cause identified.",
    "references": [],
    "priority": "high"
  }
  ```

- [ ] **Create Bug Ticket**:
  - Document issue
  - Assign to Debugger
  - High priority

- [ ] **Root Cause Analysis**:
  - What went wrong?
  - Why didn't we catch it?
  - How to prevent in future?

---

## Deployment Frequency

**Recommended**:
- Small changes: Deploy daily (if tests pass)
- Medium changes: Deploy weekly
- Large changes: Deploy every 2 weeks

**Avoid**:
- Deploying Friday evening (no team for weekend)
- Deploying during peak hours (high risk)
- Deploying multiple large changes at once (hard to debug)

---

## Deployment Types

### Hotfix Deployment

**When**: Critical bug in production, can't wait for next sprint

**Process**:
1. Create hotfix branch from main
2. Fix bug
3. Test thoroughly
4. Fast-track code review
5. Deploy immediately
6. Monitor closely

**Example**:
```bash
git checkout main
git pull
git checkout -b hotfix/critical-bug
# Fix bug
git commit -m "Hotfix: [description]"
git push
# Create PR, get emergency review
# Merge and deploy
```

### Feature Flag Deployment

**When**: Deploying risky feature, want gradual rollout

**Process**:
1. Deploy feature behind flag (disabled)
2. Verify deployment stable
3. Enable for internal team
4. Enable for 10% users
5. Enable for 50% users
6. Enable for 100% users

**Example**:
```typescript
// In code
if (config.features.newDashboard) {
  return <NewDashboard />;
} else {
  return <OldDashboard />;
}

// In environment
NEW_DASHBOARD=false  # Initially disabled
NEW_DASHBOARD=true   # Enable when ready
```

---

## Success Checklist

Deployment is successful when:

- [ ] All tests passing
- [ ] Code reviewed and approved
- [ ] Deployed without errors
- [ ] Migrations successful
- [ ] Smoke tests passing
- [ ] No new errors in logs
- [ ] Performance normal
- [ ] User feedback positive
- [ ] Monitored for 30-60 minutes
- [ ] Team notified

---

## Common Issues & Solutions

### Issue: Deployment Fails

**Symptoms**: Build fails, deployment errors

**Investigation**:
- Check deployment logs
- Check environment variables
- Check dependency versions
- Check build configuration

**Solution**:
- Fix build errors
- Update environment variables
- Update dependencies
- Correct configuration

### Issue: Database Migration Fails

**Symptoms**: Migration errors, can't connect to database

**Investigation**:
- Check migration syntax
- Check database permissions
- Check database connection
- Check for conflicting data

**Solution**:
- Fix migration SQL
- Grant permissions
- Update connection string
- Handle data conflicts

### Issue: Site Down After Deployment

**Symptoms**: 502/503 errors, timeouts

**Investigation**:
- Check server logs
- Check database connection
- Check memory/CPU usage
- Check recent code changes

**Solution**:
- Rollback deployment
- Fix underlying issue
- Redeploy

### Issue: Slow Performance

**Symptoms**: Site slow, timeouts

**Investigation**:
- Check database queries
- Check API response times
- Check bundle size
- Check caching

**Solution**:
- Optimize queries
- Add indexes
- Reduce bundle size
- Implement caching

---

**Remember**: Deploy confidently. Test thoroughly. Monitor closely. Have rollback plan ready. Document everything.

**Version**: 1.0
**Last Updated**: 2025-01-20
**Maintained By**: DevOps Engineer
