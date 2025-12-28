# Commit All Changes to Git

Commit all current changes to git with a proper commit message.

## Your Task

1. **Check Status**: Run `git status` to see all changes
2. **Review Changes**: Run `git diff` to understand what's being committed
3. **Check Recent Commits**: Run `git log --oneline -5` to match commit message style
4. **Stage All Changes**: Add all relevant files (but NOT secrets, .env files, etc.)
5. **Create Commit**: Write a clear commit message following project conventions

## Commit Message Format

```
<type>: <short description>

<optional longer description>

<optional: list of changes>

Generated with [Claude Code](https://claude.ai/code)

Co-Authored-By: Claude <noreply@anthropic.com>
```

### Types
- `feat`: New feature
- `fix`: Bug fix
- `refactor`: Code refactoring
- `docs`: Documentation changes
- `test`: Adding or updating tests
- `chore`: Maintenance tasks

## Safety Checks

Before committing, verify:
- [ ] No secrets or credentials in staged files
- [ ] No .env files being committed
- [ ] No debug/console.log statements left in
- [ ] Build passes (if applicable)
- [ ] Tests pass (if applicable)

## Files to NEVER Commit
- `.env`, `.env.local`, `.env.production`
- `credentials.json`, `secrets.json`
- `appsettings.Development.json` (if contains secrets)
- `node_modules/`, `bin/`, `obj/`
- Personal IDE settings

## After Commit

Report:
- Commit hash
- Files included
- Commit message used

Ask if user wants to push to remote.

---

*This command releases any commit hold from `/x1-git-hold`*
