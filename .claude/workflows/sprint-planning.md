# Sprint Planning Workflow (Product Manager)

Run this at the start of each sprint (typically every 2 weeks, or when current sprint completes).

**Time Required**: 90-120 minutes

---

## Pre-Planning (30 min)

### 1. Archive Previous Sprint

**If this is not the first sprint**:

- [ ] **Copy Completed Stories** to archive
  ```bash
  mkdir -p .claude/archive/sprint-XX
  cp .claude/active/stories/*.md .claude/archive/sprint-XX/
  ```

- [ ] **Extract Key Learnings** from completed stories
  - Read each story's "Key Learnings" section
  - Add significant patterns to `.claude/memory/lessons.md`
  - Remove redundant lessons

- [ ] **Create Sprint Summary** in archive
  ```markdown
  # Sprint XX Summary

  **Dates**: YYYY-MM-DD to YYYY-MM-DD
  **Goal**: [What was the sprint goal]

  ## Completed Stories
  - US001: User Authentication ✓
  - US002: Subscription Billing ✓

  ## Metrics
  - Tasks Planned: 12
  - Tasks Completed: 10 (83%)
  - Velocity: Good

  ## Key Decisions
  - Chose REST over GraphQL for dashboard API
  - Added Redis caching for user sessions

  ## Blockers Resolved
  - Stripe API keys (resolved in 1 day)
  - CORS issues (resolved same day)

  ## Lessons Learned
  - Always validate on backend (not just frontend)
  - Prisma unique constraint errors need explicit handling
  ```

- [ ] **Delete Old sprint.md**
  ```bash
  rm .claude/active/sprint.md
  ```

- [ ] **Clean Up** completed task files (they're already summarized in stories)
  ```bash
  rm .claude/active/tasks/T*.md
  ```

### 2. Review Backlog

- [ ] **Read** `.claude/memory/backlog.md` completely

- [ ] **Identify Candidates** for this sprint
  - Features aligned with roadmap
  - Bugs prioritized by severity (P0, P1)
  - Technical debt blocking progress
  - Dependencies (does item X need item Y first?)

- [ ] **Consider Velocity**
  - Last sprint: X tasks completed
  - Target: Y tasks this sprint (usually 80-100% of last sprint)
  - Don't overcommit (better to finish less than abandon more)

### 3. Check Roadmap

- [ ] **Read** `design_docs/roadmap.md`

- [ ] **Ensure Alignment**
  - Sprint contributes to quarterly goals
  - Moving toward next milestone
  - Not drifting off course

- [ ] **Identify Urgent Items**
  - User-reported critical bugs
  - Upcoming deadlines
  - Dependencies for other features

---

## Planning (60 min)

### 4. Define Sprint Goal

- [ ] **Write One-Sentence Goal**
  - What are we achieving this sprint?
  - Should be measurable/demoable

**Examples**:
- "Launch MVP authentication and user dashboard"
- "Complete subscription billing with Stripe integration"
- "Improve performance and fix top 5 user-reported bugs"

**Not**:
- "Make progress on features" (too vague)
- "Implement auth, billing, dashboard, notifications, etc." (too many goals)

### 5. Select User Stories

- [ ] **Choose 2-4 Stories** from backlog
  - Aligned with sprint goal
  - Within velocity estimate
  - Clear acceptance criteria
  - No missing dependencies

- [ ] **Create Story Files** in `.claude/active/stories/`

- [ ] **Fill In**:
  - Story definition (As a... I want... So that...)
  - Acceptance criteria
  - Technical approach
  - Architecture (files, schemas, endpoints)
  - Tasks breakdown (placeholder, will detail next)

**Story File Template**:
```markdown
# US003: User Dashboard

## Story Definition
**As a** logged-in user
**I want to** see my account dashboard
**So that** I can view my usage, billing, and settings

## Acceptance Criteria
- [ ] Dashboard shows usage statistics
- [ ] Dashboard shows billing information
- [ ] Dashboard shows account settings
- [ ] Dashboard is responsive (mobile, tablet, desktop)
- [ ] Dashboard loads in <2 seconds

## Technical Approach
**Architecture**: React dashboard with multiple widgets, backend API for data
**API Endpoints**:
- GET /api/v1/dashboard/overview
- GET /api/v1/dashboard/usage
- GET /api/v1/dashboard/billing

**Components**:
- DashboardPage
- UsageWidget
- BillingWidget
- SettingsWidget

## Tasks Breakdown
[Will fill in next step]

## Architecture
[Details after task breakdown]

## Key Decisions & Context
[Will populate as work progresses]

## Progress Log
[Agents update after sessions]

## Status
**Implementation**: Not Started
**Code Review**: Not Started
**Testing**: Not Started
```

### 6. Break Stories into Tasks

For each story:

- [ ] **Identify All Required Work**
  - Backend: Models, migrations, services, APIs
  - Frontend: Components, hooks, pages
  - Testing: Unit, integration, E2E
  - Code review

- [ ] **Create 3-8 Tasks per Story**
  - Each task: One agent, 2-6 hours, clear deliverable
  - Logical order (backend before frontend, implement before test)

- [ ] **Create Task Files** in `.claude/active/tasks/`

**Task Breakdown Example** (US003: Dashboard):
```
T015: Backend - Dashboard API endpoints (4h, Backend Dev)
T016: Frontend - Dashboard page layout (3h, Frontend Dev)
T017: Frontend - Usage widget (3h, Frontend Dev)
T018: Frontend - Billing widget (3h, Frontend Dev)
T019: E2E - Dashboard user flow (2h, Frontend Dev)
T020: Code Review - Dashboard implementation (1h, Code Reviewer)
```

**Task File Template**:
```markdown
# T015: Implement Dashboard API Endpoints

## Assignment
**Story**: US003 - User Dashboard
**Assigned To**: Backend Developer
**Priority**: P1 (High)
**Estimate**: 4 hours
**Status**: Not Started

## Requirements
Implement API endpoints for dashboard data:
- GET /api/v1/dashboard/overview (summary stats)
- GET /api/v1/dashboard/usage (usage over time)
- GET /api/v1/dashboard/billing (billing info, subscription)

## Acceptance Criteria
- [ ] All 3 endpoints implemented
- [ ] Authentication required (JWT)
- [ ] Returns JSON in consistent format
- [ ] Input validation where applicable
- [ ] Error handling comprehensive
- [ ] Integration tests for all endpoints
- [ ] API documented in task file

## Context & References
- User model: /src/server/models/user.ts
- Subscription model: /src/server/models/subscription.ts
- Auth middleware: /src/server/middleware/auth.ts
- Follow API conventions: /memory/project.md

## Dependencies
- None (can start immediately)

---

## Task Memory (Agent fills this during work)
[Investigation, decisions, implementation notes]

---

## Deliverables
- [ ] API endpoints implemented
- [ ] Integration tests written and passing
- [ ] API contract documented below

## API Documentation
[Agent fills this after implementation]

## Summary for Story Memory
[Agent fills this when complete]

## Status
**Not Started** - Ready to work
```

### 7. Assign & Prioritize

- [ ] **Assign Tasks** to appropriate agents
  - Backend tasks → Backend Developer
  - Frontend tasks → Frontend Developer
  - Review tasks → Code Reviewer
  - Balance workload across agents

- [ ] **Set Priorities**:
  - P0 (Critical): Blockers, dependencies for other work
  - P1 (High): Important features, high-priority bugs
  - P2 (Medium): Nice to have, lower-priority bugs
  - P3 (Low): Polish, tech debt

- [ ] **Identify Dependencies**
  - Mark "Depends on T###" in task files
  - Ensure dependency graph is acyclic (no circular dependencies)
  - Schedule dependencies first

**Dependency Example**:
```
T015 (Backend API) → T016 (Frontend page needs API)
T016 (Implementation) → T020 (Code Review needs implementation)
```

### 8. Create Sprint.md

- [ ] **Create** `.claude/active/sprint.md`

**Template**:
```markdown
# Sprint X (DATE_START - DATE_END)

## Goal
[One-sentence sprint goal]

## User Stories

### US001: [Story Name]
**Status**: ✅ Complete | 🚧 In Progress | 📋 Not Started | ❌ Blocked
**File**: stories/US001-name.md
**Progress**: X/Y tasks complete
**Completed**: [Date if done]

### US002: [Story Name]
**Status**: 🚧 In Progress
**File**: stories/US002-name.md
**Progress**: 3/7 tasks complete

---

## Active Tasks

### US001: [Story Name]

#### T001: [Task Name] ✅
**Assigned**: [Agent]
**Status**: Complete
**Completed**: [Date]

#### T002: [Task Name] 🚧
**Assigned**: [Agent]
**Status**: In Progress
**Started**: [Date]
**ETA**: [Date]

#### T003: [Task Name] 📋
**Assigned**: [Agent]
**Status**: Not Started
**Estimate**: 3 hours
**Depends On**: T002

#### T004: [Task Name] ❌
**Assigned**: [Agent]
**Status**: Blocked
**Blocker**: Waiting for Stripe API keys
**Blocked Since**: [Date]

### US002: [Story Name]
[Same format]

---

## Priorities
- **P0 (Critical/Blocker)**: T004 (need to unblock)
- **P1 (High)**: T002, T003, T005
- **P2 (Medium)**: T006, T007

## Size Estimates
- **Small (1-3h)**: T003, T007
- **Medium (4-6h)**: T002, T005
- **Large (7-10h)**: None this sprint

## Blockers
- **T004**: Waiting for user to provide Stripe API keys (requested [Date])

## Velocity Tracking
- **Completed This Sprint**: 3 tasks
- **Target**: 12 tasks per sprint
- **On Track**: Yes (25% complete, 20% through sprint)

## Notes
- [Any sprint-specific notes]
```

- [ ] **Verify Completeness**:
  - All stories listed
  - All tasks listed
  - Dependencies noted
  - Priorities set
  - Estimates reasonable

---

## Launch (5 min)

### 9. Announce Sprint

- [ ] **Send Message** in chat.jsonl:

```json
{
  "id": "msg_XXX",
  "timestamp": "YYYY-MM-DDTHH:MM:SSZ",
  "sender": "product-manager",
  "recipients": ["all"],
  "type": "info",
  "subject": "Sprint X Started",
  "message": "Sprint X (DATE_START - DATE_END) started. Goal: [goal]. See sprint.md for tasks. Total: X stories, Y tasks. Questions? Reply here.",
  "references": [],
  "priority": "normal"
}
```

- [ ] **Verify Agents Aware**:
  - All agents will see message at next session start
  - They'll read sprint.md and pick tasks

---

## During Sprint (Daily Check-In, 15 min)

**Every Session**:

### 10. Monitor Progress

- [ ] **Read sprint.md**:
  - Check task statuses
  - Note completed tasks (update to ✅)
  - Note in-progress tasks (update to 🚧)
  - Note blocked tasks (update to ❌)

- [ ] **Read chat.jsonl**:
  - Check for blocker messages
  - Check for decision requests
  - Check for questions

- [ ] **Identify Issues**:
  - Any agents blocked?
  - Any tasks taking longer than estimated?
  - Any dependencies causing bottlenecks?

### 11. Unblock Agents

- [ ] **Resolve Blockers**:
  - Make decisions quickly (<1 day)
  - Create tasks to unblock (e.g., "Set up Stripe account")
  - Reach out to user if needed (e.g., "Need API keys")

- [ ] **Update sprint.md**:
  - Mark blocker resolved
  - Update task status
  - Note resolution

**Goal**: No agent blocked >4 hours

### 12. Reprioritize if Needed

- [ ] **If Scope Creep**:
  - Move lower-priority items to backlog
  - Focus on sprint goal
  - Don't add new work mid-sprint (unless critical)

- [ ] **If Major Blocker**:
  - Shift priorities
  - Reassign tasks if needed
  - Adjust sprint goal if absolutely necessary (rare)

- [ ] **Update sprint.md** with any changes

---

## End of Sprint (30 min)

### 13. Review Completion

- [ ] **Mark Incomplete Tasks**:
  - Move to next sprint (if in progress)
  - Move to backlog (if not started, deprioritized)
  - Document reason (why not completed)

- [ ] **Archive Completed Stories**:
  - Follow "Archive Previous Sprint" steps above
  - Extract lessons to lessons.md

- [ ] **Calculate Velocity**:
  - Tasks planned: X
  - Tasks completed: Y
  - Velocity: Y/X (e.g., 10/12 = 83%)
  - Use for next sprint planning

### 14. Retrospective (Optional)

- [ ] **What Went Well?**:
  - Fast velocity?
  - Good collaboration?
  - Smooth deployments?

- [ ] **What Could Improve?**:
  - Too many blockers?
  - Unclear requirements?
  - Scope too large?

- [ ] **Action Items**:
  - Add process improvements to lessons.md
  - Update workflows if needed
  - Adjust estimation for next sprint

**Example Retrospective**:
```markdown
## Sprint X Retrospective

**What Went Well**:
- Backend/Frontend collaboration smooth (clear API contracts)
- Code review caught issues before production
- Deployment automated, no issues

**What Could Improve**:
- Task estimates too optimistic (actual took 20% longer)
- Too many blockers on environment setup
- Could use better E2E test coverage

**Action Items**:
- Increase estimates by 20% for similar tasks
- Create "Environment Setup" task at sprint start
- Add E2E test requirement to definition of done
```

---

## Checklist Summary

**Pre-Planning**:
- [ ] Archive previous sprint
- [ ] Review backlog
- [ ] Check roadmap

**Planning**:
- [ ] Define sprint goal
- [ ] Select user stories (2-4)
- [ ] Break into tasks (3-8 per story)
- [ ] Assign & prioritize
- [ ] Create sprint.md

**Launch**:
- [ ] Announce sprint to team

**During Sprint**:
- [ ] Monitor progress daily
- [ ] Unblock agents quickly
- [ ] Reprioritize if needed

**End of Sprint**:
- [ ] Review completion
- [ ] Calculate velocity
- [ ] Retrospective

---

## Tips for Good Sprint Planning

**Do**:
- Set clear, measurable sprint goals
- Break stories into small, independent tasks
- Leave 20% buffer (don't schedule to 100% capacity)
- Prioritize ruthlessly
- Unblock agents immediately

**Don't**:
- Overcommit (better to finish less)
- Add work mid-sprint (unless critical)
- Ignore blockers
- Skip retrospectives (miss learning opportunities)
- Plan more than 2 weeks ahead (things change)

---

**Version**: 1.0
**Last Updated**: 2025-01-20
**Maintained By**: Product Manager
