# X1 Implementation Plan

**Report:** "Using X1 Planning methodology to design implementation."

## Instructions

1. **Understand the goal** - What problem is being solved? Ask if unclear.
2. **Elicit requirements** - Capture user stories and acceptance criteria
3. **Investigate codebase** - Read relevant files, understand patterns before proposing
4. **Present high-level plan** - Architecture approach, files to modify
5. **Wait for acceptance** - Don't add detail until user approves direction
6. **Add details iteratively** - Code snippets only after codebase understood
7. **Iterate until approved** - Refine based on feedback

**Goal:** Clear requirements, codebase-aligned plan, ready for implementation.

**Speed:** Work in parallel wherever possible - read multiple files simultaneously, search client and server concurrently, research while investigating code.

---

## Phase 1: Requirements

Ask these questions (adapt as needed):

### Problem & Goal
- What problem are you solving?
- What should the end result look like?
- Who is the user/persona affected?

### Acceptance Criteria
- How will we know when this is done?
- What are the must-have vs nice-to-have requirements?

### Scope
- What's explicitly OUT of scope?
- Any constraints (time, tech, compatibility)?

### Context
- Any existing specs, designs, or references?
- Related features or code to be aware of?

---

## Phase 2: Codebase Investigation

Before proposing solutions, investigate **in parallel**:

1. **Read CLAUDE.md** - Understand project conventions
2. **Explore relevant code** - Client and server simultaneously
3. **Identify patterns** - How similar features are implemented
4. **Find reuse opportunities** - Existing services, components, utilities
5. **Research if needed** - Web search for libraries, APIs, best practices

**Parallelize:** Read multiple files at once. Search client while searching server. Research online while exploring code.

**Do NOT generate code snippets until codebase is understood.**

---

## Phase 3: High-Level Plan

Present for approval:

### Problem Statement
[1-2 sentences: what we're solving]

### Proposed Approach
[High-level architecture/design]

### Files to Create/Modify
[List with paths]

### Implementation Steps
[Ordered list, no code yet]

**Ask:** "Does this approach look right before I add details?"

---

## Phase 4: Detailed Plan (On Acceptance)

Add:
- Technical specifics
- Code snippets (aligned with codebase patterns)
- Edge cases and error handling
- Testing approach (if applicable)

### UI/UX Design Clarity (For Frontend Work)

**Before detailing any UI implementation, verify:**

1. **Visual Unity** - If multiple modes/states exist (create/edit, view/edit, loading/error):
   - Are they visually identical except for data?
   - Or intentionally different with documented reasons?
   - **Ask:** "Should [mode A] and [mode B] share the same layout?"

2. **Layout as Contract** - If an ASCII/visual diagram is provided:
   - Treat it as the **literal specification**
   - Match structure exactly, not just conceptually
   - **State:** "The layout diagram is the visual contract. Implementation will match it exactly."

3. **Mode-Specific Differences** - Document explicitly:
   - What's identical across all modes (layout, components, styling)
   - What differs per mode (data state, button labels, enabled/disabled)
   - **Ask:** "What should be different between [modes]? Everything else will be identical."

4. **Interaction Patterns** - Clarify before implementing:
   - Tap-to-edit vs always-editable inputs
   - Save-on-blur vs explicit save button
   - Inline validation vs form-level validation

**If any of these are unclear, ask before proceeding.**

### Code Snippet Rules

Apply X1 Code Review principles:

**Critical**
- Null safety - handle null/undefined
- Async safety - no race conditions, proper cancellation
- Resource safety - no leaks (dispose, unsubscribe)
- Input validation - at boundaries
- Security - no secrets, no injection vulnerabilities

**Architecture**
- Correct layer - business logic in services, not controllers
- Reuse existing - don't duplicate what exists
- Follow conventions - match project patterns
- Proper DI - correct lifetimes, registrations

**Quality**
- YAGNI - only what's needed now
- Simplicity - 10 lines beats 100
- No premature abstraction - interfaces need 2+ implementations
- Clear names - reveal intent

---

## Phase 5: Iterate

- Refine based on feedback
- Update plan sections as needed
- Continue until user says "ready to implement"

---

## Plan Output Format

Structure plan sections to align with what `/x1-review-plan` will validate:

### Problem Statement
[What we're solving - 1-2 sentences]

### Requirements Coverage
- **User Stories:** [List with acceptance criteria]
- **Success Criteria:** [How we know it's done - measurable]

### Scope
- **In scope:** [list]
- **Out of scope:** [list]

### Architecture Approach
- **Design:** [High-level approach]
- **Layer placement:** [Where code goes]
- **Reuse:** [Existing components to leverage]

### Technical Design (if applicable)
- **Database:** [Schema changes, migrations]
- **API:** [Endpoints, request/response]
- **Error Handling:** [Strategy for failures]

### UI/UX Design (if applicable)
- **Layout:** [ASCII diagram or reference - THIS IS THE VISUAL CONTRACT]
- **Visual Unity:** [All modes share same layout? If not, why?]
- **Mode Differences:** [Only: data state, button labels, visibility - NOT layout]
- **Interaction Pattern:** [Tap-to-edit, save-on-blur, inline validation, etc.]

### Security & Authorization (if applicable)
- **Auth requirements:** [Who can access]
- **Multi-tenancy:** [Tenant isolation approach]

### Files to Create/Modify
| File | Action | Purpose |
|------|--------|---------|
| path/to/file | Create/Modify | Brief description |

### Implementation Steps
1. [Step with detail]
2. [Step with detail]

### Code Snippets
[Only after codebase understood - aligned with existing patterns]

### Edge Cases & Error Handling
[Failure modes considered - what happens when X fails?]

### Testing Approach (if applicable)
[How to verify]

---

## Red Flags to Avoid in Plans

- "Will tackle separately" - Include it or explicitly scope out
- "Should work" - Verify with codebase investigation
- "Simple change" - Still needs proper error handling
- Code snippets without reading codebase - Will miss patterns
- Skipping user acceptance - Leads to rework
- No error handling strategy - Review will reject
- Missing success criteria - Can't measure done

**UI/UX Red Flags:**
- "Create mode" vs "Edit mode" without specifying if layout differs - Ask first
- Layout diagram treated as "suggestion" - It's the contract
- Different structures for different modes without explicit requirement - Default to unified
- Assuming traditional form for create, display-only for view - Clarify interaction pattern

---

## When Complete

Report:
- Requirements captured (user stories + acceptance criteria)
- Architecture approach approved by user
- Files to create/modify identified
- Code snippets aligned with codebase (if detailed plan)

**Next step:** `/x1-review-plan` to validate before implementation

---

## Congruence with x1-review-plan

This plan output is structured so `/x1-review-plan` can validate:

| Plan Section | Review Checklist |
|--------------|------------------|
| User Stories + Acceptance Criteria | Requirements Coverage |
| Architecture Approach + Layer Placement | Architecture Compliance |
| Database + API + Error Handling | Technical Design Quality |
| UI/UX Design (layout, unity, modes) | UI/UX Design Clarity |
| Auth + Multi-tenancy | Security & Authorization |
| Files to Create/Modify | File Organization |
| Edge Cases | Chaos Demon "What If" |
