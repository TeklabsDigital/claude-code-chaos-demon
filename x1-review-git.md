# X1 Review Git Changes

**Report:** "Using X1 Code Review methodology to review uncommitted git changes."

## Instructions

1. Run `git diff` and `git status` to identify all uncommitted changes
2. Apply the **full X1 Code Review checklist** (see `/x1-code-review`) to all changed files
3. Work in parallel - review multiple files simultaneously, search codebase for patterns concurrently
4. Check for code reuse opportunities - search the codebase to verify nothing is being duplicated
5. **FIX issues immediately** - don't just identify them
6. After all fixes, re-audit to ensure no new issues were introduced
7. Summarize what was fixed and confirm ready for commit

**Goal:** Ensure all uncommitted changes pass X1 Code Review before commit. Work fast by parallelizing reviews and searches.

---

## Scope

Review ALL uncommitted changes:
- Modified files (`git diff`)
- New files (`git status --porcelain | grep "^??"`)
- Staged changes (`git diff --cached`)

---

## Review Methodology

Apply the complete **X1 Code Review** checklist from `/x1-code-review`:

### Chaos Demon Mindset
- **YAGNI**: Remove code not needed TODAY
- **Simplicity**: 10 lines beats 100 if it does the same thing
- **Delete more than you add**: If in doubt, delete it
- **No gold-plating**: Remove "future enhancement" markers

### Critical Issues (Must Fix)
- Null/undefined safety violations
- Race conditions in async/concurrent code
- Security vulnerabilities (SQL injection, XSS, secrets in code)
- Resource leaks
- Data integrity issues

### Warning Issues (Should Fix)
- Architecture violations (business logic in controllers)
- Performance issues (N+1 queries, unbounded collections)
- Missing error handling
- Type safety issues
- Missing tenant isolation checks

### Cleanup Issues (Nice to Fix)
- Over-engineering (interfaces with 1 implementation)
- Code quality (unclear names, magic numbers, dead code)
- Inconsistent patterns

**See `/x1-code-review` for complete checklist including .NET and React/TypeScript specifics.**

---

## Code Reuse Check

Before approving changes, verify:
- Similar functionality doesn't already exist elsewhere
- Existing services, utilities, and patterns are reused
- No duplication of logic that exists in the codebase

**Search the codebase in parallel** to find existing implementations.

---

## Output Format

For each issue found:
```
[Critical/Warning/Cleanup] file:line - Brief description
  Problem: What's wrong
  Fix: What to do (then implement the fix)
```

---

## Final Report

- Files reviewed (list all uncommitted files)
- Issues found and fixed by severity:
  - Critical: [count]
  - Warning: [count]
  - Cleanup: [count]
- Files modified during review
- Status: **Ready for commit** OR **Blockers remaining**

**DO NOT commit** - wait for explicit approval or `/x1-git-commit` command.

---

## Relationship to Other Commands

- Uses: `/x1-code-review` methodology
- Follows: User makes changes
- Precedes: `/x1-git-commit` to commit reviewed changes
