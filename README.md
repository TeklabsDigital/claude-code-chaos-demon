# Claude Chaos Demon

A collection of custom skills (slash commands) for [Claude Code](https://claude.com/claude-code) that implement a rigorous software development workflow using the Chaos Demon methodology. These commands enforce quality gates, code reviews, and testing strategies to catch bugs before they reach production.

## Overview

The X1 workflow is a structured approach to software development that emphasizes:

- **Planning before coding** - Understand requirements and investigate the codebase first
- **Chaos Demon methodology** - Actively try to break code and find edge cases
- **Quality gates** - Review plans and code before committing
- **Iterative refinement** - Get approval at each stage before proceeding

## Why X1?

Practically, X1 is a namespace prefix that groups related commands together, making them easy to discover and invoke in Claude Code.

Philosophically, X represents the unknown - the bugs, edge cases, and failure modes hiding in your code. The Chaos Demon methodology is about hunting these unknowns before they reach production. X1 means "find the unknowns first."

## Installation

1. Clone this repository
2. Copy the `.md` files to your Claude Code skills directory:
   - **Project-level**: `.claude/commands/` in your project root
   - **User-level**: `~/.claude/commands/` for global availability

Skills are invoked in Claude Code using slash commands (e.g., `/x1-plan`).

## Commands

### Planning & Design

| Command | Description |
|---------|-------------|
| `/x1-plan` | Problem discovery, user stories, high-level plan, then detailed plan with traceable Implementation Checklist |
| `/x1-review-plan` | Validate an implementation plan using Chaos Demon methodology before coding begins |

### Implementation

| Command | Description |
|---------|-------------|
| `/x1-implement` | Implement from approved plan with completeness tracking and blocking completion gate |
| `/x1-audit-plan` | Post-implementation gap analysis — verify 100% plan coverage |

### Code Review

| Command | Description |
|---------|-------------|
| `/x1-code-review` | Full code review methodology covering security, architecture, performance, and quality |
| `/x1-review-git` | Review uncommitted git changes using X1 Code Review standards |

### Testing

| Command | Description |
|---------|-------------|
| `/x1-review-tests` | Review existing tests or generate new test plans |
| `/x1-test-plan` | Strategic test planning template for systematic bug hunting |

### Git Workflow

| Command | Description |
|---------|-------------|
| `/x1-git-commit` | Commit changes with proper message formatting and safety checks |
| `/x1-git-hold` | Activate commit hold - prevents automatic commits until explicitly approved |

### Session Management

| Command | Description |
|---------|-------------|
| `/x1-handover` | Generate a context handover prompt to continue work in a new session |

## Workflow

A typical development workflow using these commands:

```
1. /x1-plan          → Discover problem, user stories, high-level plan, then detailed plan with Implementation Checklist
2. /x1-review-plan   → Validate plan with Chaos Demon methodology
3. /x1-implement     → Implement with completeness tracking + completion gate
4. /x1-audit-plan    → Verify 100% plan coverage (safety net — should find 0 gaps)
5. /x1-code-review   → Review implementation for bugs and issues
6. /x1-review-tests  → Review or generate tests
7. /x1-review-git    → Final review of all changes
8. /x1-git-commit    → Commit with proper message
```

Use `/x1-git-hold` at the start of a session to prevent accidental commits until you're ready.

Use `/x1-handover` when ending a session to generate a prompt for continuing in a new context window.

## Chaos Demon Philosophy

### Origins: Inspired by Chaos Engineering

The Chaos Demon methodology is inspired by [Chaos Engineering](https://en.wikipedia.org/wiki/Chaos_engineering), pioneered by Netflix with their [Chaos Monkey](https://netflix.github.io/chaosmonkey/) tool in 2010. After a major database outage, Netflix realized that systems don't fail often enough during development - so they built tools to deliberately break things in production.

Netflix's philosophy was radical: **randomly terminate production servers** to force engineers to build resilient systems. This evolved into the "Simian Army" - Chaos Gorilla (kill entire data centers), Latency Monkey (inject network delays), and more. Today, major companies including Google, Amazon, Microsoft, and Facebook practice Chaos Engineering.

### Shifting Left: From Production to Development

While traditional Chaos Engineering tests **running systems** in production, the Chaos Demon methodology applies the same adversarial mindset **earlier in the development lifecycle**:

| Chaos Engineering (Netflix) | Chaos Demon (X1 Workflow) |
|---------------------------|------------------|
| Randomly terminate production servers | Systematically attack code and plans |
| Test infrastructure resilience | Test code correctness and design |
| Applied to running systems | Applied during code review and planning |
| Find failures in production | Find failures before code is written |

The core insight: **it's cheaper to find bugs in a plan than in code, and cheaper to find them in code than in production**.

### The Adversarial Mindset

> Your job is NOT to validate that code works. Your job is to **find every possible way it will fail in production**.

Key principles:

- **Break assumptions** - What did the developer assume would never happen?
- **Push boundaries** - Test at extremes: zero, negative, max values, null, empty
- **Race for races** - Force bad timing in concurrent operations
- **Inject chaos** - Failures, randomness, delays at the worst possible moments
- **Never mask bugs** - If something feels wrong, investigate it

### Why This Approach Works

**1. Bugs are exponentially cheaper to fix early**

The cost of fixing a bug increases dramatically as it moves through the development lifecycle:

| Stage | Relative Cost |
|-------|---------------|
| Requirements/Planning | 1x |
| Development | 6x |
| Testing | 15x |
| Production | 100x |

By applying adversarial thinking during plan review, we catch design flaws before writing any code.

**2. Developers are naturally optimistic**

When writing code, developers think about the happy path - valid inputs, successful responses, available resources. The Chaos Demon forces consideration of:

- What if the database is slow? Down? Full?
- What if the user clicks submit twice? Ten times?
- What if the input is null? Empty? 10MB of garbage?
- What if two requests modify the same record simultaneously?

**3. Code reviews miss what they don't look for**

Traditional code reviews focus on "does this code do what it's supposed to do?" The Chaos Demon asks "what will make this code fail?" This systematic approach catches entire categories of bugs that casual review misses:

- Race conditions and concurrency issues
- Resource exhaustion and memory leaks
- Security vulnerabilities (injection, authorization bypass)
- Edge cases and boundary conditions

**4. Production failures are predictable**

Most production incidents fall into known categories: null references, timeouts, resource exhaustion, concurrency bugs, missing error handling. The Chaos Demon checklists encode these patterns, ensuring every review considers the most common failure modes.

**5. It builds institutional knowledge**

The structured checklists capture hard-won lessons from past incidents. New team members benefit from accumulated experience without having to learn from their own production failures.

### The "What If" Checklist

For every component, the Chaos Demon asks:

**Network Failures:**
- What if network is slow? (10 second latency)
- What if network drops mid-request?
- What if the external API is down?

**Data Validation:**
- What if input is null or empty?
- What if input is 10MB of data?
- What if input contains `<script>` or `'; DROP TABLE --`?

**Concurrency:**
- What if two requests happen simultaneously?
- What if user clicks submit twice?
- What if a background job runs while user is modifying data?

**Resource Limits:**
- What if 1000 users hit this endpoint simultaneously?
- What if the response is 100MB?
- What if memory usage grows unbounded?

**Observability:**
- How do we know this is broken in production?
- What logs will help debug this?
- How do we reproduce production issues locally?

## Code Review Checklist Highlights

### Critical (Must Fix)
- Null/undefined safety violations
- Race conditions in async/concurrent code
- SQL injection, XSS, secrets in code
- Resource leaks (connections, streams, subscriptions)
- Fallback code that hides failures

### Architecture
- Controllers calling repositories directly (bypass services)
- Business logic in controllers
- Missing tenant isolation checks
- Wrong layer placement

### Performance
- N+1 queries
- Unbounded collections
- Missing pagination
- Loading entire tables

## Customization

These skills are designed for .NET and React/TypeScript projects but can be adapted:

1. Modify the language-specific sections in `/x1-code-review`
2. Adjust the architecture layer references to match your stack
3. Update date/time handling rules for your conventions

## Requirements

- [Claude Code CLI](https://claude.com/claude-code)
- Skills support enabled in Claude Code

## License

This work is licensed under [Creative Commons Attribution-NonCommercial 4.0 International (CC BY-NC 4.0)](https://creativecommons.org/licenses/by-nc/4.0/).

### You are free to:

- **Share** - Copy and redistribute the material in any medium or format
- **Adapt** - Remix, transform, and build upon the material

### Under the following terms:

- **Attribution** - You must give appropriate credit, provide a link to the license, and indicate if changes were made
- **NonCommercial** - You may not use the material for commercial purposes without obtaining a commercial license

### Commercial Licensing

**Free for:** Personal projects, educational use, and non-commercial open source projects.

**Commercial use requires a license.** If you're using these commands in a commercial product, for-profit service, or enterprise environment, please contact us:

- **Email:** office@reqwiseconsulting.com
- **Website:** [www.reqwiseconsulting.com](https://www.reqwiseconsulting.com)

## Contributing

Contributions welcome. Please ensure any changes:
- Follow the existing Chaos Demon philosophy
- Add concrete failure scenarios, not vague guidelines
- Include specific, actionable fixes for issues identified
