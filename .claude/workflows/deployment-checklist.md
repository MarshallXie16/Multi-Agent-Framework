# Deployment Checklist

Follow for every production deployment.

---

## Pre-Deployment

- [ ] All tests passing
- [ ] Code reviewed and approved  
- [ ] Environment variables configured
- [ ] Database backup created
- [ ] Reviewed migrations (no destructive changes)

## Deployment

- [ ] Deploy backend first
- [ ] Run database migrations
- [ ] Deploy frontend
- [ ] Smoke test (login, critical flows)

## Post-Deployment

- [ ] Monitor logs (10+ minutes)
- [ ] Check error tracking
- [ ] Verify no user reports
- [ ] Document deployment

## Rollback Plan

**If issues arise**:
1. Revert code commit
2. Redeploy previous version
3. Restore database backup if needed
4. Create bug ticket for investigation

---

**Timing**: Deploy during low-traffic, team available
**Communication**: Announce before/after deployment
