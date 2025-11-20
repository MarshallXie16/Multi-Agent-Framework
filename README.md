# Multi-Agent Framework for Autonomous SaaS Development

A framework enabling specialized AI agents to collaboratively build SaaS products with minimal coordination overhead.

## Overview

Solves three critical challenges in AI-driven development:

1. **Clear Direction**: PM maintains backlog/sprint with clear acceptance criteria
2. **Context Management**: 3-level memory system (project → story → task)
3. **Quality Control**: Mandatory code review + curated lessons

**Philosophy**: 90% coding, 10% coordination. Communicate only when blocked. Progressive context loading.

## Quick Start

### 1. Clone as Template

```bash
git clone https://github.com/your-org/multi-agent-framework.git my-saas-project
cd my-saas-project
```

### 2. Customize Project Memory

Edit `.claude/memory/project.md`:
- Replace [placeholders] with your project details
- Define your tech stack
- Establish your code conventions
- Document your architecture

### 3. Fill Design Docs

Fill in `design_docs/`:
- `business_plan.md`: Your market and business model
- `product_design.md`: Product features and architecture
- `roadmap.md`: Development phases
- `user_stories.md`: Initial stories

### 4. Start First Sprint

As Product Manager:
1. Add initial stories to `.claude/memory/backlog.md`
2. Run sprint planning (see `.claude/workflows/sprint-planning.md`)
3. Create first sprint in `.claude/active/sprint.md`

### 5. Start Building

Agents will:
1. Read session start protocol
2. Check sprint.md for tasks
3. Pick highest priority task
4. Implement → Test → Document → Request review

## Agent Roles

- **Product Manager** (Sonnet): Task decomposition, sprint planning, unblocking, decisions
- **UI/UX Designer** (Sonnet): Component specs, design system, UX audits
- **Frontend Developer** (Sonnet): UI implementation, client logic, testing
- **Backend Developer** (Sonnet): APIs, database, business logic, testing
- **Code Reviewer** (Opus): Quality gate, can make minor fixes, enforce standards
- **Debugger** (Opus): Deep investigation, root cause analysis
- **DevOps Engineer** (Sonnet): CI/CD, deployment, infrastructure, secrets

## Memory System

**Level 1 - Project Memory** (`.claude/memory/project.md`):
- Load once per session
- Max 500 lines
- Tech stack, conventions, architecture

**Level 2 - Story Memory** (`.claude/active/stories/US###.md`):
- Context for a user story
- PM creates, agents update
- Archive when complete

**Level 3 - Task Memory** (`.claude/active/tasks/T###.md`):
- Context for ONE task
- Agents work here
- Delete after completion

## Communication (JSONL)

Messages in `.claude/communication/chat.jsonl`:

```json
{
  "sender": "backend-developer",
  "recipients": ["frontend-developer"],
  "type": "handoff",
  "message": "API ready for integration",
  "references": ["US001", "T003"],
  "priority": "normal"
}
```

**When to communicate**: Blockers and important decisions only
**When NOT to communicate**: Status updates, implementation details

## Workflows

- **Sprint Planning**: `.claude/workflows/sprint-planning.md`
- **Code Review**: `.claude/workflows/code-review-checklist.md`
- **Debugging**: `.claude/workflows/debugging-protocol.md`
- **Deployment**: `.claude/workflows/deployment-checklist.md`

## Best Practices

**For All Agents**:
- Read session start protocol every session
- Update task memory as you work
- Check lessons.md when stuck
- Communicate blockers immediately
- Functions <100 lines, tests required

**For Product Manager**:
- Unblock first, plan second
- Clear over fast
- Trust agents on implementation
- 80% sprint capacity

**For Developers**:
- Read specs first
- Progressive context loading
- Test as you build
- Document cross-domain changes

**For Code Reviewer**:
- Fix minor (<10 lines), request major
- Security first
- Tests must pass
- Consistent standards

## Troubleshooting

**"Don't know what to work on"**: Read sprint.md → Pick highest priority task for your role

**"Blocked by another agent"**: Send blocker in chat.jsonl → PM resolves <4 hours

**"Can't find information"**: Read project.md → lessons.md → grep/glob → Ask via chat

**"Tests failing"**: Read output → Run locally → Fix → If complex, tag Debugger

## Success Metrics

Framework working when:
- Agents know what to work on (<5% idle)
- Context loads in <5 minutes
- Code review catches issues (<5% post-merge bugs)
- Communication light (<5 messages/day/agent)
- 90% time coding, 10% coordinating

---

**Version**: 1.0  
**Philosophy**: Minimal overhead, maximum autonomy, quality first
