# Debugging Protocol

Systematic approach to investigating and fixing complex bugs. Follow these steps when debugging issues.

**Time Budget**: Maximum 2 hours per phase (total 8-10 hours for complex bugs). If exceeding, escalate to PM.

---

## Step 1: Reproduce (30 min max)

**Goal**: Get the bug to occur consistently and reliably.

### Read Context

- [ ] Read blocker message/bug report completely
- [ ] Read task file for implementation context
- [ ] Read story file for feature context
- [ ] Check when issue was first reported

### Follow Exact Steps

- [ ] **Execute Reproduction Steps** exactly as described
  ```
  Example:
  1. Go to /dashboard
  2. Click "Refresh" button 5+ times rapidly
  3. Dashboard freezes, console shows error
  ```

- [ ] **Document Environment**:
  - Browser/version (if frontend)
  - OS
  - Node version (if backend)
  - Database state/data
  - Environment variables
  - Any special conditions

### Reproduce Consistently

- [ ] Try reproduction steps 3-5 times
- [ ] Bug should occur every time (or understand pattern)
- [ ] If can't reproduce:
  - Check environment differences
  - Ask reporter for more details
  - Try different browsers/devices
  - Check with different data

### Document Reproduction

- [ ] **Write Down Exact Steps**:
  ```markdown
  ## Reproduction Steps (Confirmed)
  1. Log in as user with 100+ widgets
  2. Navigate to /dashboard
  3. Click "Refresh" button rapidly (5 times in <2 seconds)
  4. Dashboard freezes
  5. Console error: "Maximum update depth exceeded"

  **Environment**:
  - Browser: Chrome 120.0.6099.109
  - OS: macOS 14.2
  - Data: User ID 123 (has 150 widgets)
  - Frequency: 100% reproducible with these steps
  ```

- [ ] Add to task file under "Debugging Investigation"

---

## Step 2: Isolate (30-60 min)

**Goal**: Narrow down where the issue occurs (file, function, line).

### Binary Search Approach

- [ ] **Identify Scope**:
  - Which file(s) are involved?
  - Which function(s)?
  - Frontend or backend or both?

- [ ] **Narrow Down**:
  - Comment out half the code
  - Still crashes? Bug in other half
  - Doesn't crash? Bug in commented half
  - Repeat until isolated to small section

**Example**:
```typescript
function processUser(user) {
  validateUser(user);          // Try commenting out
  enrichUserData(user);        // Try commenting out
  saveUser(user);              // Try commenting out
  sendEmail(user);             // Try commenting out
}

// Comment out bottom half
// Still crashes? Bug in validateUser or enrichUserData
// Repeat until found
```

### Add Logging

- [ ] **Strategic Logging**:
  ```typescript
  console.log('1. Starting calculation');
  console.log('2. Input:', items);

  const total = items.reduce((sum, item) => {
    console.log('3. Processing item:', item, 'current sum:', sum);
    return sum + item.price * item.quantity;
  }, 0);

  console.log('4. Final total:', total);
  return total;
  ```

- [ ] Run and check logs
- [ ] Identify where it goes wrong

### Use Debugger

- [ ] **Add Breakpoints** in suspected area:
  - Browser DevTools (frontend)
  - Node debugger (backend)

- [ ] Step through code line by line
- [ ] Watch variables
- [ ] Identify exact line where bug occurs

### Check Stack Trace

- [ ] **Read Error Stack Trace** completely:
  ```
  Error: Cannot read property 'length' of undefined
    at validatePassword (auth.ts:42)
    at signup (auth.ts:15)
    at router.post (routes.ts:23)
  ```

- [ ] Start from top of stack (deepest function)
- [ ] Work backward to find root cause

### Identify Trigger

- [ ] **What Conditions Cause Bug?**:
  - Specific input values?
  - Timing (race condition)?
  - State (what needs to be true)?
  - External factors (network, database)?

**Example**:
```
Bug only happens when:
- User has >20 widgets (small number works fine)
- Rapid clicks (<2 seconds between clicks)
- Specific to Dashboard component
```

---

## Step 3: Root Cause (30-60 min)

**Goal**: Understand WHY the bug happens, not just WHAT.

### Ask "Why?"

- [ ] **Why did this happen?**:
  - Not just "variable is undefined"
  - But "why is variable undefined?"
  - And "why wasn't this caught?"

**Example**:
```
WHAT: "state.user is undefined"
WHY: "useEffect updates state.user, but render happens before useEffect runs"
DEEPER WHY: "Missing null check before accessing state.user"
ROOT WHY: "Component assumes user is always loaded (incorrect assumption)"
```

### Check Git History

- [ ] **When Was This Introduced?**:
  ```bash
  # Find commits that touched this file
  git log --oneline src/components/Dashboard.tsx

  # Show changes in specific commit
  git show abc123

  # Or use git bisect to find exact commit
  git bisect start
  git bisect bad          # Current commit (bug exists)
  git bisect good abc123  # Older commit (bug didn't exist)
  # Git will check out commits, test each, mark good/bad
  ```

- [ ] Identify commit that introduced bug
- [ ] Read commit message and diff
- [ ] Understand what changed and why

### Read Related Code

- [ ] **Is This a Pattern?**:
  - Same bug in other places?
  - Copy-pasted code with same issue?
  - Systemic problem or one-off?

- [ ] **Check Similar Functions**:
  - Do they have same bug?
  - Do they handle edge case correctly?
  - Can we learn from them?

### Check Lessons

- [ ] **Read** `lessons.md`:
  - Have we seen this before?
  - Is there prevention guidance?
  - Did we forget to apply a lesson?

---

## Step 4: Fix (30-60 min)

**Goal**: Implement minimal fix that solves root cause.

### Design Fix

- [ ] **Consider Options**:
  - Option 1: [Description, pros, cons]
  - Option 2: [Description, pros, cons]
  - Option 3: [Description, pros, cons]

- [ ] **Choose Best Option**:
  - Solves root cause (not just symptom)
  - Minimal change (don't refactor everything)
  - Clear and maintainable
  - No side effects

**Example**:
```
Bug: Dashboard infinite re-render due to missing useEffect dependency

Option 1: Add dependency to useEffect
Pros: Minimal change, fixes issue
Cons: Might cause other re-renders

Option 2: Move logic outside useEffect
Pros: Cleaner, no dependency issues
Cons: Larger refactor

Option 3: Memoize expensive computation
Pros: Solves root cause, prevents future issues
Cons: Slightly more complex

CHOSEN: Option 3 (best long-term solution)
```

### Implement Fix

- [ ] **Make Minimal Change**:
  ```typescript
  // Before (buggy)
  useEffect(() => {
    setData(processWidgets(widgets));
  }, [widgets]); // Missing: processWidgets causes re-render

  // After (fixed)
  const processedWidgets = useMemo(() => {
    return processWidgets(widgets);
  }, [widgets]);

  useEffect(() => {
    setData(processedWidgets);
  }, [processedWidgets]);
  ```

- [ ] Don't refactor unrelated code
- [ ] Focus on fixing the bug

### Test Fix

- [ ] **Manual Test**:
  - Run reproduction steps
  - Bug should be gone
  - No new bugs introduced

- [ ] **Edge Cases**:
  - Test with different data
  - Test boundary conditions
  - Test related functionality

- [ ] **Regression Test** (next step)

---

## Step 5: Add Regression Test (15-30 min)

**Goal**: Prove bug is fixed and prevent recurrence.

### Write Test That Would Have Caught Bug

- [ ] **Test Fails Without Fix**:
  ```typescript
  // This test should fail on buggy code, pass on fixed code
  it('should not infinite loop with rapid clicks', () => {
    render(<Dashboard widgets={largeWidgetSet} />);
    const refreshButton = screen.getByText('Refresh');

    // Rapid clicks (triggers bug in old code)
    for (let i = 0; i < 10; i++) {
      fireEvent.click(refreshButton);
    }

    // Should not throw (would throw in old code)
    expect(screen.getByText('Dashboard')).toBeInTheDocument();
  });
  ```

- [ ] Verify test actually catches the bug
- [ ] Remove fix → test fails
- [ ] Add fix → test passes

### Test Edge Cases

- [ ] **Add Tests for Edge Cases**:
  ```typescript
  it('should handle empty widget array', () => {
    render(<Dashboard widgets={[]} />);
    expect(screen.getByText('No widgets')).toBeInTheDocument();
  });

  it('should handle large widget array', () => {
    const largeSet = Array(1000).fill({ name: 'Widget' });
    render(<Dashboard widgets={largeSet} />);
    expect(screen.getByText('Dashboard')).toBeInTheDocument();
  });
  ```

### Run All Tests

- [ ] **Run Full Test Suite**:
  ```bash
  npm test
  ```

- [ ] All tests should pass
- [ ] No new failures introduced

---

## Step 6: Document (15-30 min)

**Goal**: Capture findings for future reference and learning.

### Update Task File

- [ ] **Add Investigation Report** to task file:
  ```markdown
  ## Debugging Investigation

  **Debugger**: [Your role]
  **Date**: YYYY-MM-DD
  **Time Spent**: X hours

  ### Reproduction Steps
  [Exact steps]

  ### Investigation Process
  [What you tried, what you found]

  ### Root Cause
  [Why it happened]

  ### Fix
  [What you changed and why]

  ### Tests Added
  [Regression tests]

  ### Key Findings
  [Important discoveries]

  ### Recommendations
  [How to prevent in future]
  ```

### Add to Lessons (If Significant)

- [ ] **Check If Lesson-Worthy**:
  - Non-obvious root cause?
  - Pattern that could recur?
  - Prevents entire class of bugs?
  - Significant impact?

- [ ] **Add to** `lessons.md`:
  ```markdown
  ## [Category]

  ### [Bug Title]
  **Symptom**: [What users/developers saw]
  **Root Cause**: [Why it happened]
  **Solution**: [How it was fixed]
  **Prevention**: [How to avoid in future]
  ```

**Example**:
```markdown
## Frontend

### React useEffect Infinite Loop
**Symptom**: Component freezes, "Maximum update depth exceeded" error
**Root Cause**: useEffect missing dependency, state update causes re-render, effect runs again
**Solution**: Memoize computed values with useMemo, include all dependencies in useEffect
**Prevention**: Enable exhaustive-deps ESLint rule. Test with large datasets and rapid interactions.
```

### Send Resolution Message

- [ ] **Notify Requester**:
  ```json
  {
    "sender": "debugger",
    "recipients": ["frontend-developer", "product-manager"],
    "type": "resolution",
    "subject": "T015 Bug Resolved - Dashboard Infinite Loop",
    "message": "Investigated dashboard freeze. Root cause: useEffect missing dependency causing infinite re-render. Fixed by memoizing processWidgets. Added regression test. See detailed investigation in T015 task file. Frontend can resume work.",
    "references": ["T015"],
    "priority": "high"
  }
  ```

---

## Common Bug Patterns & Solutions

### Pattern 1: Null/Undefined Error

**Symptom**: "Cannot read property 'x' of undefined"

**Investigation**:
```typescript
// Add logging
console.log('user:', user); // undefined
console.log('user.name:', user?.name); // Error before this line
```

**Common Causes**:
- Missing null check
- API returned null
- Async race condition (variable not loaded yet)

**Fix**:
```typescript
// Before
return user.name;

// After
if (!user) {
  throw new Error('User not found');
}
return user.name;

// Or
return user?.name || 'Unknown';
```

### Pattern 2: N+1 Query

**Symptom**: Slow API response, many database queries

**Investigation**:
```typescript
// Enable Prisma query logging
const prisma = new PrismaClient({
  log: ['query'],
});

// Check logs, see pattern:
// SELECT * FROM users
// SELECT * FROM posts WHERE authorId = 1
// SELECT * FROM posts WHERE authorId = 2
// SELECT * FROM posts WHERE authorId = 3
// ... (N queries)
```

**Fix**:
```typescript
// Before (N+1)
const users = await prisma.user.findMany();
for (const user of users) {
  user.posts = await prisma.post.findMany({
    where: { authorId: user.id }
  });
}

// After (1 query)
const users = await prisma.user.findMany({
  include: { posts: true }
});
```

### Pattern 3: Race Condition

**Symptom**: Intermittent failures, works sometimes

**Investigation**:
```typescript
// Add timing logs
console.log('1. Starting data fetch');
fetchData().then(() => console.log('2. Data fetched'));
console.log('3. Rendering UI');
// Logs: 1, 3, 2 (wrong order!)
```

**Fix**:
```typescript
// Before
let data = null;
fetchData().then(result => { data = result; });
renderUI(data); // data is still null!

// After
const data = await fetchData();
renderUI(data); // data is loaded
```

### Pattern 4: Memory Leak

**Symptom**: Application slows over time, crashes after hours

**Investigation**:
- Use Chrome DevTools Memory profiler
- Take heap snapshots over time
- Check for growing object counts
- Look for event listeners not cleaned up

**Fix**:
```typescript
// Before
useEffect(() => {
  window.addEventListener('resize', handleResize);
  // Missing cleanup!
});

// After
useEffect(() => {
  window.addEventListener('resize', handleResize);
  return () => {
    window.removeEventListener('resize', handleResize);
  };
}, []);
```

---

## When to Escalate

**Escalate to PM if**:
- Can't reproduce after 1 hour
- Root cause unclear after 2 hours investigation
- Fix requires major refactor (>100 lines)
- Impacts production and need immediate decision
- Need user input or clarification

**Escalate to User if**:
- Need reproduction steps
- Need access to specific environment
- Need data/configuration details

---

## Debugging Tools

### Frontend
- **Chrome DevTools**: Elements, Console, Network, Performance, Memory
- **React DevTools**: Component tree, props, state, profiler
- **Redux DevTools**: State changes over time
- **console.log()**: Strategic logging
- **debugger;**: Breakpoints in code

### Backend
- **Node Debugger**: `node --inspect`, Chrome DevTools
- **console.log()**: Strategic logging
- **Prisma logging**: Query logging
- **curl/Postman**: Test API endpoints directly
- **Database tools**: pgAdmin, DataGrip

### Git
- **git log**: See commit history
- **git show**: Show specific commit
- **git bisect**: Find commit that introduced bug
- **git blame**: See who changed each line

---

## Success Checklist

Before closing bug:

- [ ] Bug reproduced consistently
- [ ] Root cause identified (not just symptom)
- [ ] Minimal fix implemented
- [ ] Regression tests added
- [ ] All tests passing
- [ ] Investigation documented in task file
- [ ] Lessons added (if applicable)
- [ ] Resolution message sent

---

**Remember**: Be systematic, not random. Fix root causes, not symptoms. Test thoroughly. Document findings. Help the team learn.

**Version**: 1.0
**Last Updated**: 2025-01-20
**Maintained By**: Debugger
