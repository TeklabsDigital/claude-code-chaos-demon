# X1 Implementation Plan

**Report:** "Using X1 Planning methodology to design implementation."

## Instructions

1. **Discover the problem** - Spend time here. Don't rush to solutions.
2. **Challenge and think laterally** - Be a thinking partner, not a yes-machine.
3. **Elicit user stories** - At least one with acceptance criteria before proceeding.
4. **Present high-level plan** - Conceptual approach, no code, no file paths.
5. **Wait for acceptance** - Don't investigate codebase until direction approved.
6. **Investigate codebase** - Only after high-level accepted. Find patterns, reuse.
7. **Add detail iteratively** - Technical specifics, code snippets, Implementation Checklist.
8. **Iterate until approved** - Refine based on feedback.

**Goal:** Clear problem understanding, codebase-aligned plan with traceable Implementation Checklist, ready for `/x1-implement`.

**Speed:** Work in parallel wherever possible - read multiple files simultaneously, search client and server concurrently, research while investigating code.

---

## Phase 1: Problem Discovery

Spend time here. Don't rush to solutions. Understand the problem domain.

### What problem are you solving?
- What's the current state? What's wrong with it?
- What's the desired state? What does success look like?
- Who is affected? What's the impact?

### Why does this matter?
- What happens if we don't solve this?
- What's the cost of the current state?
- Are there upstream/downstream effects?

### What have you already tried or considered?
- Previous approaches and why they didn't work
- Constraints you've already identified
- Related work or prior art

### Probe deeper — ask the questions the user hasn't thought of:
- "You mentioned X — what happens when Y?"
- "Who else is affected by this besides the obvious users?"
- "What's the simplest version of this that would still be valuable?"
- "Is there a reason this hasn't been solved before?"
- "What would make this problem go away entirely vs just managing it?"

### Challenge and think laterally

**Be a critical thinking partner, not a yes-machine.**

- If an idea seems over-engineered, say so: "This could work, but have you considered [simpler approach]?"
- If the problem can be reframed, offer alternatives: "Another way to look at this is..."
- If the user is solving a symptom, probe the root cause: "Is the real problem actually X rather than Y?"
- Think out-of-the-box — propose approaches the user hasn't considered
- Present trade-offs honestly: "Approach A is simpler but limits X. Approach B is more work but gives you Y."
- If something seems like a bad idea, push back respectfully: "I'd caution against that because [reason]. Here's what I'd suggest instead."
- If the user still wants their approach after hearing alternatives, respect that — it's their codebase

**The most valuable thing in early planning is divergent thinking. Don't converge on a solution too quickly. Explore the problem space.**

**DO NOT propose final solutions in this phase. Understand and challenge first.**

### User Story Gate (BLOCKING)

**At least one user story with acceptance criteria is REQUIRED before proceeding.**

If the user hasn't provided user stories, help them formulate them:
- "Based on what you've described, here's a user story: As a [role], I want [capability] so that [benefit]. Does this capture it?"
- Acceptance criteria must be specific and testable
- Each user story drives the Implementation Checklist items

**DO NOT proceed to Phase 2 without at least one approved user story.**

User stories are the foundation of the entire X1 workflow:
- Plan items trace back to user stories
- Implementation Checklist items map to acceptance criteria
- Tests verify acceptance criteria are met
- Audit verifies traceability end-to-end

---

## Phase 2: High-Level Plan

Present the approach at a conceptual level. No code. No file paths. Just the shape of the solution.

### Problem Statement
[1-2 sentences: what we're solving and why]

### User Stories
[List with acceptance criteria — approved in Phase 1]

### Proposed Approach
[Conceptual design — how will we solve this? What's the strategy?]

### Scope
- **In scope:** [list]
- **Out of scope:** [list]

### Key Decisions
[Architectural choices, trade-offs, alternatives considered]

**Ask:** "Does this direction feel right? Any concerns before I investigate the codebase and add detail?"

**Wait for acceptance before proceeding to Phase 3.**

---

## Phase 3: Codebase Investigation

Only after the high-level approach is accepted. Investigate **in parallel**:

1. **Read CLAUDE.md** - Understand project conventions
2. **Explore relevant code** - Client and server simultaneously
3. **Identify patterns** - How similar features are implemented
4. **Find reuse opportunities** - Existing services, components, utilities
5. **Research if needed** - Web search for libraries, APIs, best practices

**Parallelize:** Read multiple files at once. Search client while searching server. Research online while exploring code.

**Do NOT generate code snippets until codebase is understood.**

---

## Phase 4: Detailed Plan (On Acceptance)

Add technical specifics that align with the codebase:
- Technical design details
- Code snippets (aligned with codebase patterns)
- Edge cases and error handling
- Testing approach
- Implementation Checklist (IMP-NNN items)

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

### Implementation Checklist

Every deliverable item numbered for traceability. This is the **contract** — `/x1-implement` must satisfy every item.

#### Files
- IMP-001: Create `path/to/file.cs` — [purpose] → US-1
- IMP-002: Modify `path/to/existing.cs` — [what changes] → US-1

#### Features / Business Logic
- IMP-003: [Feature/rule description] → US-1 AC-1
- IMP-004: [Feature/rule description] → US-1 AC-2

#### Infrastructure (DI, config, migrations)
- IMP-005: Register `ServiceName` in DI container → US-1
- IMP-006: Add migration for [schema change] → US-1

#### Error Handling & Edge Cases
- IMP-007: Handle [failure scenario] in [location] → US-1 AC-3
- IMP-008: Validate [input] at [boundary] → US-2 AC-1

#### Tests
- IMP-009: Unit test — [scenario] → US-1 AC-1
- IMP-010: Integration test — [scenario] → US-2 AC-1

Each IMP item traces to a User Story (US-N) and optionally an Acceptance Criterion (AC-N).

**Total: N items. Implementation is complete when all items are ✅, ⏭️ (justified), or ➖.**

**Traceability check:** Every AC must have at least one IMP item. If an AC has no IMP items, the plan is incomplete.

---

## Red Flags to Avoid in Plans

- "Will tackle separately" - Include it or explicitly scope out
- "Should work" - Verify with codebase investigation
- "Simple change" - Still needs proper error handling
- Code snippets without reading codebase - Will miss patterns
- Skipping user acceptance - Leads to rework
- No error handling strategy - Review will reject
- Missing success criteria - Can't measure done
- Missing user stories - Can't trace requirements to implementation
- No Implementation Checklist - Can't verify completeness

**UI/UX Red Flags:**
- "Create mode" vs "Edit mode" without specifying if layout differs - Ask first
- Layout diagram treated as "suggestion" - It's the contract
- Different structures for different modes without explicit requirement - Default to unified
- Assuming traditional form for create, display-only for view - Clarify interaction pattern

---

## When Complete

Report:
- Problem clearly articulated
- User stories captured with acceptance criteria
- Architecture approach approved by user
- Files to create/modify identified
- Code snippets aligned with codebase (if detailed plan)
- Implementation Checklist with numbered IMP-NNN items

**Next step:** `/x1-review-plan` to validate before implementation

---

## Congruence with X1 Workflow

This plan output is structured so downstream skills can validate:

| Plan Section | Downstream Skill |
|--------------|------------------|
| User Stories + Acceptance Criteria | `/x1-review-plan` Requirements Coverage |
| Architecture Approach + Layer Placement | `/x1-review-plan` Architecture Compliance |
| Database + API + Error Handling | `/x1-review-plan` Technical Design Quality |
| UI/UX Design (layout, unity, modes) | `/x1-review-plan` UI/UX Design Clarity |
| Auth + Multi-tenancy | `/x1-review-plan` Security & Authorization |
| Files to Create/Modify | `/x1-review-plan` File Organization |
| Edge Cases | `/x1-review-plan` Chaos Demon "What If" |
| Implementation Checklist (IMP items) | `/x1-implement` Completion Gate |
| Implementation Checklist (IMP items) | `/x1-audit-plan` Gap Analysis |
