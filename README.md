# Multi-Agent Framework for Autonomous SaaS Development

A comprehensive framework enabling specialized AI agents to collaboratively build a SaaS product with minimal coordination overhead.

## Overview

This framework solves three critical challenges in AI-driven development:

1. **Clear Direction**: PM maintains backlog and sprint, agents pull clear tasks with acceptance criteria
2. **Context Management**: 3-level memory system (project → story → task) with progressive loading
3. **Quality Control**: Mandatory code review, curated lessons, flexible ownership with documentation

### Core Philosophy

- **90% coding, 10% coordination**: Agents spend most time building, minimal overhead
- **Communicate only when blocked**: Structured communication for decisions and blockers only
- **Trust + verify**: Autonomy with mandatory code review
- **Progressive context loading**: Read what you need, when you need it

## Quick Start

### 1. Clone This Repository

```bash
git clone https://github.com/your-org/multi-agent-framework.git my-saas-project
cd my-saas-project
```

### 2. Customize Project Memory

Edit `.claude/memory/project.md`:
- Replace templates with your project details
- Define your tech stack
- Establish your code conventions
- Document your architecture decisions

### 3. Create Initial Design Docs

Fill in `design_docs/`:
- `business_plan.md`: Your market analysis and business model
- `product_design.md`: Product features and architecture
- `roadmap.md`: Development phases and milestones
- `user_stories.md`: Initial user stories

### 4. Start First Sprint

As Product Manager agent:
1. Create initial user stories in `.claude/memory/backlog.md`
2. Run sprint planning (see `.claude/workflows/sprint-planning.md`)
3. Create first sprint in `.claude/active/sprint.md`

### 5. Start Building

Agents will:
1. Read their session start protocol (in `CLAUDE.md`)
2. Check sprint.md for tasks
3. Pick highest priority task
4. Implement, test, document
5. Request code review

## Framework Structure

```
project-root/
├── .claude/                      # Agent coordination hub
│   ├── CLAUDE.md                 # Shared base system prompt (all agents read this)
│   ├── agents/                   # Agent-specific prompts
│   │   ├── product-manager.md
│   │   ├── ui-ux-designer.md
│   │   ├── frontend-developer.md
│   │   ├── backend-developer.md
│   │   ├── code-reviewer.md
│   │   ├── debugger.md
│   │   └── devops-engineer.md
│   ├── workflows/                # Process checklists
│   │   ├── code-review-checklist.md
│   │   ├── sprint-planning.md
│   │   ├── debugging-protocol.md
│   │   └── deployment-checklist.md
│   ├── memory/                   # Persistent knowledge
│   │   ├── project.md            # Project overview (load once per session)
│   │   ├── lessons.md            # Past mistakes & solutions
│   │   └── backlog.md            # Future work (PM manages)
│   ├── active/                   # Current sprint work
│   │   ├── sprint.md             # Sprint index (PM creates)
│   │   ├── stories/              # User story files
│   │   └── tasks/                # Task files (agents work here)
│   └── communication/
│       └── chat.jsonl            # Team messages (JSONL format)
│
├── design_docs/                  # Static design documentation
│   ├── business_plan.md
│   ├── technical_requirements.md
│   ├── product_design.md
│   ├── roadmap.md
│   ├── user_stories.md
│   └── components/               # UI component specs (Designer creates)
│
├── docs/                         # User-facing documentation
│   ├── api/
│   ├── guides/
│   └── README.md
│
└── src/                          # Your application code
    ├── client/                   # Frontend
    ├── server/                   # Backend
    └── shared/                   # Shared code
```

## Agent Roles

### 1. Product Manager (Sonnet)
**Responsibilities**: Task decomposition, sprint planning, unblocking agents, decisions
- Breaks user stories into tasks
- Maintains sprint.md and backlog.md
- Makes cross-domain decisions
- Extracts lessons from completed work

### 2. UI/UX Designer (Sonnet)
**Responsibilities**: Component specs, design system, UX audits
- Creates detailed component specifications
- Maintains design system
- Audits frontend for consistency
- **Note**: Creates specs, doesn't implement

### 3. Frontend Developer (Sonnet)
**Responsibilities**: UI implementation, client logic, API integration, testing
- Implements React components from specs
- Client-side logic and state management
- API integration with custom hooks
- Unit and E2E tests

### 4. Backend Developer (Sonnet)
**Responsibilities**: APIs, database, business logic, testing
- RESTful API implementation
- Database schema design (Prisma)
- Business logic in services
- Unit and integration tests

### 5. Code Reviewer (Opus)
**Responsibilities**: Quality gate, can make minor fixes, enforce standards
- Reviews all code before merge
- Can fix minor issues (<10 lines)
- Requests changes for major issues
- Enforces security and performance standards

### 6. Debugger (Opus)
**Responsibilities**: Deep investigation, root cause analysis, complex bugs
- Invoked when agents hit complex bugs
- Systematic debugging protocol
- Regression tests for all fixes
- Postmortems for significant bugs

### 7. DevOps Engineer (Sonnet)
**Responsibilities**: CI/CD, deployment, infrastructure, secrets
- Manages deployment pipelines
- Handles environment configuration
- Manages secrets and API keys
- Monitors infrastructure

## Memory System

### Level 1: Project Memory (`.claude/memory/project.md`)

**Purpose**: Everything needed to understand the project
**Max Size**: 500 lines
**When to Read**: Beginning of every session (skim)
**Contains**:
- Tech stack
- Directory structure
- Code conventions
- Architecture decisions
- Critical file pointers

### Level 2: Story Memory (`.claude/active/stories/US###-name.md`)

**Purpose**: Complete context for a user story
**Lifecycle**: PM creates → Agents update → Complete → Archive
**Contains**:
- Story definition and acceptance criteria
- Technical approach
- Task breakdown
- Progress log (updated after each session)
- Key decisions and blockers

### Level 3: Task Memory (`.claude/active/tasks/T###-description.md`)

**Purpose**: Full context for ONE task
**Lifecycle**: Pick → Work → Complete → Summarize → Delete
**Contains**:
- Requirements and acceptance criteria
- Context and references
- Task Memory section (agent fills during work)
- Deliverables and summary

## Communication System

### When to Communicate

**Send Message When**:
- Blocked by another agent's work
- Important decision needed (affects multiple domains)

**Don't Communicate For**:
- Implementation details (agent decides)
- Status updates (use sprint.md)
- Questions answerable by reading code

### Message Format (JSONL)

Messages in `.claude/communication/chat.jsonl` use JSON Lines format:

```json
{
  "id": "msg_001",
  "timestamp": "2024-01-15T10:30:00Z",
  "sender": "backend-developer",
  "recipients": ["frontend-developer"],
  "type": "handoff",
  "subject": "Auth API Ready",
  "message": "Implemented /signup and /login endpoints. Ready for frontend integration.",
  "references": ["US001", "T003"],
  "priority": "normal"
}
```

**Message Types**: handoff, blocker, question, decision, decision-final, response, resolution, info

## Workflows

### Sprint Planning (`product-manager`)

1. Archive previous sprint
2. Review backlog
3. Define sprint goal
4. Select 2-4 user stories
5. Break into tasks (3-8 per story)
6. Assign and prioritize
7. Create sprint.md
8. Announce to team

See `.claude/workflows/sprint-planning.md` for full process.

### Code Review (`code-reviewer`)

1. Read context (story, task files)
2. Run tests (must pass)
3. Review code (use checklist)
4. Make minor fixes (<10 lines)
5. Request major changes (or approve)

See `.claude/workflows/code-review-checklist.md` for full checklist.

### Debugging (`debugger`)

1. **Reproduce** (30 min): Get bug to occur consistently
2. **Isolate** (60 min): Narrow to file, function, line
3. **Root Cause** (60 min): Understand why, not just what
4. **Fix** (60 min): Minimal fix for root cause
5. **Test** (30 min): Regression test
6. **Document** (30 min): Update lessons if significant

See `.claude/workflows/debugging-protocol.md` for full process.

### Deployment (`devops-engineer`)

**Pre-Deployment**:
- All tests passing
- Code reviewed
- Environment configured
- Database backed up

**Deploy**:
- Backend first
- Run migrations
- Frontend second
- Smoke test

**Post-Deployment**:
- Monitor logs (10+ minutes)
- Verify metrics
- Document deployment

See `.claude/workflows/deployment-checklist.md` for full checklist.

## Best Practices

### For All Agents

1. **Read session start protocol** (in CLAUDE.md) every session
2. **Update task memory** as you work (investigation, decisions, issues)
3. **Check lessons.md** when stuck (we may have solved this before)
4. **Communicate blockers immediately** (don't waste time stuck)
5. **Follow code quality standards** (functions <100 lines, tests required, etc.)

### For Product Manager

- **Clear over fast**: Ambiguous tasks create more work
- **Unblock first**: Prioritize unblocking agents over new planning
- **Trust agents**: Don't micromanage implementation details
- **Sustainable pace**: Don't overload sprints (aim for 80% capacity)

### For Developers

- **Read specs first**: Designer specs for UI, story files for requirements
- **Progressive context loading**: Don't read entire codebase upfront
- **Test as you build**: Write tests alongside implementation
- **Cross-domain work**: Can modify other domains if justified (document why)

### For Code Reviewer

- **Thorough but constructive**: Find issues but suggest solutions
- **Fix minor, request major**: Avoid ping-pong for typos
- **Security first**: Never approve code with vulnerabilities
- **Consistent standards**: Apply same standards to all code

## Success Metrics

The framework is working when:
- Agents know what to work on (<5% blocked by unclear direction)
- Context loads in <5 minutes per session
- Code review catches issues (<5% post-merge bugs)
- Communication stays light (<5 messages per day per agent)
- Agents spend 90% of time coding, 10% on coordination

## Troubleshooting

### "I don't know what task to work on"

1. Read `.claude/active/sprint.md` for assigned tasks
2. Pick highest priority unblocked task for your role
3. If no tasks: Check with PM via chat or review backlog

### "I'm blocked by another agent"

1. Send blocker message in chat.jsonl
2. Create task in backlog to unblock (if possible)
3. Switch to another unblocked task in meantime
4. PM will resolve blocker quickly (<4 hours target)

### "I can't find the information I need"

1. **Project context**: Read `.claude/memory/project.md`
2. **Code patterns**: Check lessons.md for similar work
3. **Feature context**: Read story file for current task
4. **Code location**: Use grep/glob to find files
5. **Still stuck**: Ask via chat (don't waste hours)

### "Tests are failing"

1. Read test output carefully
2. Check if recent changes broke tests
3. Run tests locally to reproduce
4. Fix issues found
5. If complex bug: Tag Debugger in chat

## Migrating from Single-Agent Framework

If you have an existing single-agent framework:

1. **Copy memory.md** → Split into:
   - Project overview → `.claude/memory/project.md`
   - Lessons learned → `.claude/memory/lessons.md`

2. **Copy tasks.md** → Reorganize as:
   - Current work → `.claude/active/sprint.md` + task files
   - Future work → `.claude/memory/backlog.md`

3. **Create design docs** from existing documentation

4. **Run first sprint planning** to create initial sprint

## Contributing to the Framework

Found an issue or improvement?

1. Update relevant file (CLAUDE.md, agent prompts, workflows)
2. Test with actual usage
3. Document change in commit message
4. Submit pull request

## License

[Add your license here]

## Support

- **Documentation**: Read files in `.claude/` and `design_docs/`
- **Issues**: Create GitHub issue with details
- **Questions**: Check lessons.md for common patterns

---

**Version**: 1.0
**Last Updated**: 2025-01-20

Built with the philosophy: Minimal overhead, maximum autonomy, quality first.
