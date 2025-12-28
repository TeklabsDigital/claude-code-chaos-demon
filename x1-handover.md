# Context Handover Prompt Generator

**Purpose**: Generate a handover prompt to continue work in the next context window.

---

## Instructions

Analyze the current conversation and generate a comprehensive handover prompt that can be copy-pasted to start a new session. The prompt should enable seamless continuation of work.

**Output the handover prompt directly to chat** - do NOT create any files.

---

## Handover Prompt Structure

Generate the following sections:

### 1. Session Summary (2-3 sentences)
What was the primary focus of this session? What problem were we solving?

### 2. Completed Work
List what was accomplished with specific file paths:
- Files created (with paths)
- Files modified (with paths)
- Key decisions made

### 3. Current State
- Build status (passing/failing with errors if any)
- Current branch
- Any pending changes (uncommitted work)

### 4. In-Progress Work
What was actively being worked on when context ended?
- Current task
- What's left to do
- Any blockers or issues

### 5. Key Context
Critical information needed to continue:
- Architecture decisions made
- Important file locations
- Relevant plan files or specs

### 6. Next Steps
Clear actionable items to continue work.

---

## Output Format

Output the handover as a fenced code block that can be copy-pasted:

```
[HANDOVER PROMPT]

## Session Summary
...

## Completed Work
...

## Current State
- Build: [PASSING/FAILING]
- Branch: [branch-name]
- Uncommitted: [yes/no]

## In-Progress Work
...

## Key Files
...

## Next Steps
1. ...
2. ...
```

---

## Important

- Be concise but complete
- Include specific file paths
- Note any errors or blockers
- Reference plan files if they exist
- Do NOT create any files - output to chat only
