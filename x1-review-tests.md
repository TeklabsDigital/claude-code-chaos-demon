# X1 Review / Generate Tests

**Report:** "Using X1 Test Plan methodology."

## Mode Selection

Ask: **"Review existing tests OR generate new test plan?"**

---

## Mode 1: Review Existing Tests

### Instructions
1. Identify test files related to the feature/code
2. Evaluate test coverage and quality
3. Identify missing test scenarios
4. **FIX or ADD tests** as needed
5. Re-audit after changes

### Test Quality Checklist
- Tests verify CORRECT behavior (not just current behavior)
- Tests have clear Arrange-Act-Assert structure
- Test names describe what is being tested
- Edge cases covered (null, empty, boundary values)
- No flaky tests (random failures)

---

## Mode 2: Generate Test Plan

### Instructions
1. Identify code to test (from git changes, specific files, or implementation plan)
2. Generate comprehensive test scenarios using Chaos Demon methodology
3. Create actual test files with test cases
4. Run tests and verify they pass

---

## Chaos Demon Test Categories

### Null & Empty Chaos
- Null objects, empty strings, whitespace-only, missing properties
- Expected: 400 Bad Request or ArgumentException

### Boundary & Extreme Values
- Zero, negative, max int, empty pagination
- Dates in far past/future, string max length + 1
- Unicode, special characters, null bytes

### Concurrency & Race Conditions
- Parallel create/update/delete operations
- Shared state in parallel code
- Transaction isolation violations

### Data Integrity & Validation
- Invalid enum values, SQL injection, XSS
- Foreign key violations, duplicate unique keys
- Invalid state transitions

### Permission & Security
- No auth token, expired token, wrong role
- Cross-tenant data access (CRITICAL)
- IDOR (Insecure Direct Object Reference)

### Performance & Resource Exhaustion
- Large datasets without pagination
- N+1 query problems
- Connection pool exhaustion

### Error Handling & Recovery
- Database failures, API timeouts
- Partial batch failures
- Transaction rollbacks

## Test Case Template

```csharp
[Fact]
public async Task MethodName_Scenario_ExpectedBehavior()
{
    // Arrange
    var mockRepo = new Mock<IRepository>();
    // Setup...

    // Act
    var result = await service.MethodAsync(input);

    // Assert
    Assert.NotNull(result);
    mockRepo.Verify(...);
}
```

## Important Principles

- If implementation has a bug, **FIX THE IMPLEMENTATION** first
- Never write tests that accommodate bugs
- Tests should EXPOSE bugs, not work around them
- Aim for 80%+ coverage on critical paths

## Final Report

- Test scenarios identified
- Tests created/modified
- Test results (pass/fail)
- Coverage summary
