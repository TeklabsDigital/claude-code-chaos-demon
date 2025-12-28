# Implementation Plan Quality Assurance Template

**Purpose**: Use this template to audit implementation plans for completeness, accuracy, and alignment with the project requirements and goals

**When to Use**: After creating an implementation plan but BEFORE writing any code.

---

## Review Philosophy: The Chaos Demon Approach

**CRITICAL MINDSET**: When reviewing implementation plans, you MUST adopt the "Chaos Demon" mentality:

### Think Like a Production Incident Waiting to Happen

Your job is NOT to validate the plan. Your job is to **FIND EVERY POSSIBLE WAY THIS PLAN WILL FAIL IN PRODUCTION**.

#### The Chaos Demon's Questions

For every architectural decision, ask:
- **"What happens when..."** (the network fails, the database is slow, the user clicks twice, Azure times out)
- **"What if..."** (the data is malformed, the API returns 10MB, the user retries, two requests happen simultaneously)
- **"How does this break..."** (when deployed, when scaled, when users abuse it, when dependencies fail)
- **"Where's the exception handling?"** (malformed JSON, network timeout, database lock, OutOfMemoryException)
- **"What about edge cases?"** (empty lists, null values, missing fields, concurrent access, retry storms)

#### Specific Production Failure Scenarios to Consider

**Distributed Systems Failures:**
- Network partitions (client → server, server → database, server → external API)
- Timeouts (SSE stream timeout, database query timeout, external API timeout)
- Partial failures (3 of 5 operations succeed, then system crashes)
- Out-of-order message delivery (events arrive in wrong sequence)
- Message loss (events dropped due to network issues)

**Resource Exhaustion:**
- Memory leaks (unbounded buffers, caching without limits, event accumulation)
- Connection pool exhaustion (too many concurrent requests, connections not released)
- Thread pool starvation (blocking I/O on thread pool threads)
- CPU spikes (regex catastrophic backtracking, inefficient algorithms at scale)
- Disk space exhaustion (unbounded logging, temp file accumulation)

**Concurrency Issues:**
- Race conditions (two users modifying same entity)
- Deadlocks (transaction A waits for B, B waits for A)
- Lost updates (read-modify-write without locking)
- DbContext lifetime violations (disposed mid-stream, shared across threads)
- Cache coherency (stale data after update)

**Security Vulnerabilities:**
- Authorization bypasses (user A accesses user B's data)
- Injection attacks (SQL injection, XSS, command injection via tool arguments)
- Rate limiting missing (abuse of expensive operations)
- Audit logging gaps (can't trace who did what)
- Sensitive data leakage (passwords in logs, PII in error messages)

**Data Integrity Failures:**
- Orphaned records (parent deleted, child references invalid ID)
- Partial writes (stream fails midway, database has incomplete data)
- Duplicate data (idempotency missing, retry creates duplicate)
- Referential integrity violations (foreign key constraints violated)
- Schema migration failures (old code runs against new schema)

#### Red Flags That Demand Deep Scrutiny

🚩 **"Will tackle separately"** → Translation: "Not going to do it, production will break"
🚩 **"Should work"** → Translation: "I haven't thought through edge cases"
🚩 **"Simple refactoring"** → Translation: "30 files changed, 100 bugs introduced"
🚩 **"No breaking changes"** → Translation: "I don't understand the dependencies"
🚩 **"Estimated 2-3 days"** → Translation: "Actually 2-3 weeks, I'm optimistic"
🚩 **No exception handling** → Translation: "First production error will crash the system"
🚩 **No logging strategy** → Translation: "Good luck debugging production issues"
🚩 **No rollback plan** → Translation: "Deploy and pray"
🚩 **"Big Bang migration"** → Translation: "All users break at once"
🚩 **"Testing is out of scope"** → Translation: "We're shipping bugs"

#### Concrete Examples of Chaos Demon Questions

**For Event Streaming Architecture:**
```
Plan says: "Provider streams events to orchestrator"

CHAOS DEMON ASKS:
❌ What if provider streams 1000 events/second and orchestrator processes 10/second? (backpressure)
❌ What if event #47 is malformed JSON? (skip it? crash? log error?)
❌ What if events arrive out of order? (how do we reorder? or do we guarantee ordering?)
❌ What if stream is interrupted midway? (partial response? retry? resume?)
❌ What if orchestrator dies mid-stream? (where does the data go? database half-written?)
```

**For Database Operations:**
```
Plan says: "Update database with message and thread"

CHAOS DEMON ASKS:
❌ What transaction boundary? (before stream? after stream? mid-stream?)
❌ What if database write fails after user saw response? (user thinks it succeeded, but DB has nothing)
❌ What if two requests create the same thread simultaneously? (unique constraint violation? last write wins?)
❌ What if DbContext is disposed while stream is active? (ObjectDisposedException)
❌ What about connection pooling? (30-second stream holds connection, pool exhausted after 100 concurrent users)
```

**For External API Calls:**
```
Plan says: "Enrich citations via external service"

CHAOS DEMON ASKS:
❌ What if service is down? (return un-enriched? fail entire request? timeout?)
❌ What if service takes 30 seconds to respond? (user waited for entire stream, then timeout? what's the max wait?)
❌ What if service returns 500 error? (retry? how many times? exponential backoff?)
❌ What if service returns malformed data? (validate response? fallback?)
❌ What if service rate-limits us? (circuit breaker? queue requests?)
```

**For Tool Execution:**
```
Plan says: "Execute tool and continue streaming"

CHAOS DEMON ASKS:
❌ What if tool execution takes 60 seconds? (SSE timeout? stream dies?)
❌ What if tool returns 10MB of data? (exceed Azure API limits? how do we truncate?)
❌ What if tool throws exception? (catch it? log it? return error to AI? continue streaming?)
❌ What if AI calls same tool 100 times? (infinite loop? rate limiting? max depth?)
❌ What about authorization? (can user execute this tool on this resource?)
```

#### The "What If" Checklist (Run Through EVERY Time)

For EVERY architectural component, ask:

**Network Failures:**
- [ ] What if network is slow? (10 second latency)
- [ ] What if network drops mid-request?
- [ ] What if DNS resolution fails?
- [ ] What if SSL certificate is invalid?

**Data Validation:**
- [ ] What if input is null?
- [ ] What if input is empty string?
- [ ] What if input is 10MB of data?
- [ ] What if input contains special characters? (`<script>`, SQL injection, path traversal)
- [ ] What if input is wrong type? (string instead of number)

**Concurrency:**
- [ ] What if two requests happen simultaneously?
- [ ] What if user clicks submit twice?
- [ ] What if background job runs while user is modifying data?
- [ ] What if migration runs while app is processing requests?

**Dependencies:**
- [ ] What if external API is down?
- [ ] What if external API changes response format?
- [ ] What if database is at max connections?
- [ ] What if cache is stale?
- [ ] What if Azure OpenAI deprecates the API?

**Resource Limits:**
- [ ] What if response is 1MB? 10MB? 100MB?
- [ ] What if 1000 users hit this endpoint simultaneously?
- [ ] What if user leaves browser open for 24 hours?
- [ ] What if memory usage grows unbounded?

**Observability:**
- [ ] How do we know this is broken in production?
- [ ] What logs will help debug this?
- [ ] What metrics should we track?
- [ ] How do we reproduce production issues locally?

#### Review Output Style

When you find issues, be SPECIFIC and CONCRETE:

**❌ BAD (vague):**
```
"Error handling is missing"
```

**✅ GOOD (concrete):**
```
CRITICAL BLOCKER #14: NO EXCEPTION HANDLING FOR SSE PARSING

Line 589: StreamEventsAsync() has no try/catch block.

PRODUCTION FAILURE SCENARIO:
1. Azure returns malformed JSON (API bug, network corruption)
2. JsonException thrown during SSE parsing
3. Exception propagates to ASP.NET Core
4. Entire request fails with 500 error
5. User sees error after waiting 30 seconds for stream

MISSING:
- try/catch around SSE parsing
- Fallback: yield StreamErrorEvent on JsonException
- Logging: log malformed JSON for debugging
- Metrics: track SSE parsing failure rate
- Retry policy: should we retry on transient failures?

EXAMPLE FIX:
try {
    var data = sseEvent.ParseJson();
} catch (JsonException ex) {
    _logger.LogError(ex, "Malformed SSE event: {RawData}", sseEvent.RawData);
    yield return new StreamErrorEvent("parse_error", "Malformed server response", Retryable: false);
    yield break;
}
```

#### Expected Review Outcome

A thorough Chaos Demon review should:
- Find 10-30+ critical issues (if plan is complex)
- Provide concrete failure scenarios for each issue
- Suggest specific fixes with code examples
- Result in a REJECTED status if blockers exist
- Force the author to think through every edge case

**Remember**: It's better to find 50 issues in review than 1 critical bug in production.

---

## QA Checklist

### 1. Document Structure & Metadata

- [ ] Version number specified
- [ ] Date specified
- [ ] Status clearly marked (Draft, Review, Approved, In Progress, Completed)
- [ ] Related Business Spec referenced with correct file path
- [ ] Executive Summary provides clear overview of what will be built
- [ ] Table of Contents is present and accurate

**Issues Found:**

```
[Document any missing or incorrect metadata]
```

---

### 2. Business Specification Alignment

#### 2.1 Requirements Coverage

- [ ] All user stories from business spec are addressed in implementation
- [ ] All acceptance criteria have corresponding technical implementations
- [ ] All data requirements from business spec are included in data models
- [ ] Success criteria from business spec are measurable in implementation

**Missing Coverage:**

```
[List any user stories or requirements not addressed]
```

#### 2.2 Scope Adherence

- [ ] Implementation plan stays within "In Scope" boundaries
- [ ] No features from "Out of Scope" are included
- [ ] Any scope changes are documented and justified
- [ ] Gold-plating avoided (no unnecessary features)

**Scope Violations:**

```
[List any out-of-scope features or unnecessary additions]
```

---

### 3. Architecture & Design Patterns

#### 3.1 Architecture Compliance

**Frontend (adapt to your stack):**

- [ ] Component organization follows project conventions
- [ ] Page/view organization is consistent
- [ ] State management approach is consistent (Context, Redux, Zustand, etc.)
- [ ] Server state management follows patterns (React Query, SWR, etc.)
- [ ] Authentication follows established patterns
- [ ] Form validation is consistent across the app
- [ ] Reuses existing UI components before creating new ones

**Backend (adapt to your stack):**

- [ ] Follows layer separation:
  - **API/Controllers** → thin, delegate to Services
  - **Services** → business logic, orchestration
  - **Persistence/Data** → database access, repositories
  - **Domain/Models** → entities, DTOs (standalone, no dependencies)
  - **Infrastructure** → external integrations, cross-cutting concerns
- [ ] ORM/data access follows established patterns
- [ ] Authentication/authorization follows project patterns
- [ ] Background jobs/workers follow established patterns

**Architecture Violations:**

```
[List any violations of established architecture patterns]
```

#### 3.2 Data Access Patterns

- [ ] ORM/data access layer used consistently (EF Core, Dapper, etc.)
- [ ] Entities stored in appropriate layer (Domain/Models)
- [ ] DbContext/repositories in Persistence layer
- [ ] Migrations strategy documented
- [ ] Audit fields included where appropriate (`CreatedBy`, `CreatedAt`, `UpdatedBy`, `UpdatedAt`)
- [ ] Services layer handles data transformation and business logic

**Data Access Issues:**

```
[List any data access pattern violations]
```

#### 3.3 Security & Authorization

- [ ] Authentication requirements specified
- [ ] Authorization attributes/middleware planned
- [ ] Role-based access control configured
- [ ] Compliance considerations addressed (audit logs, PII protection)
- [ ] Input validation and sanitization specified
- [ ] Multi-tenancy isolation (if applicable)

**Security Gaps:**

```
[List any security concerns or missing authorization]
```

---

### 4. Technical Design Quality

#### 4.1 Database Schema

- [ ] All entities have proper primary keys
- [ ] Foreign key relationships defined
- [ ] Index strategy specified for performance
- [ ] Migration strategy documented (use `dotnet ef migrations add`)
- [ ] Entities placed in `Project.Models` project
- [ ] DbContext configuration in `Project.Data`

**Schema Issues:**

```
[List any database design concerns]
```

#### 4.2 API Design

- [ ] RESTful conventions followed (`GET /api/resource`, `POST /api/resource`)
- [ ] URL structure consistent with existing patterns
- [ ] Request/response DTOs defined in `Project.Models` or `Project.Services/DTO`
- [ ] Pagination strategy for list endpoints
- [ ] Error response format standardized
- [ ] Swagger/OpenAPI documentation updated

**API Design Issues:**

```
[List any API design problems]
```

#### 4.3 Service Layer Design

- [ ] Services contain business logic (not in controllers)
- [ ] Services registered with proper lifecycle (Scoped, Singleton, Transient)
- [ ] Interface-based design for testability
- [ ] Async/await used for I/O operations
- [ ] Proper error handling and logging
- [ ] Transaction boundaries clearly defined

**Service Design Issues:**

```
[List any service layer concerns]
```

---

### 5. Code Quality & Best Practices

#### 5.1 Error Handling

- [ ] Exception handling strategy defined
- [ ] User-friendly error messages planned
- [ ] Logging strategy specified (what to log, when, at what level)
- [ ] No sensitive data in logs or error responses
- [ ] Graceful degradation for non-critical failures

**Error Handling Gaps:**

```
[List any error handling concerns]
```

#### 5.2 Performance Considerations

- [ ] Database query optimization planned (indexes, pagination)
- [ ] Caching strategy defined (if applicable)
- [ ] Parallel processing considered for bulk operations
- [ ] Connection pooling and resource management addressed
- [ ] Performance requirements measurable and testable

**Performance Concerns:**

```
[List any performance red flags]
```

#### 5.3 Maintainability

- [ ] Code follows existing naming conventions
- [ ] Separation of concerns maintained (no mixing of layers)
- [ ] No tight coupling between components
- [ ] Configuration externalized (appsettings.json, not hardcoded)
- [ ] Magic numbers and strings replaced with constants

**Maintainability Issues:**

```
[List any maintainability concerns]
```

---

### 6. Implementation Details

#### 6.1 Code Placement & Project Structure

**Backend Architectural Layers (adapt paths to your project):**

- [ ] **Controllers/API** - Thin controllers that delegate to services
- [ ] **Services** - Business logic, interfaces + implementations
- [ ] **Persistence/Data** - DbContext, repositories, migrations
- [ ] **Domain/Models** - Entities, DTOs, value objects
- [ ] **Infrastructure** - External integrations, cross-cutting concerns
- [ ] **Integration** - External API clients (optional layer)

**Frontend Architectural Layers (adapt paths to your project):**

- [ ] **Pages/Views** - Route components
- [ ] **Components** - Reusable UI components
- [ ] **State Management** - Context providers, stores, hooks
- [ ] **API Clients** - Backend communication
- [ ] **Types** - TypeScript type definitions
- [ ] **Utilities** - Helper functions, formatters
- [ ] **Assets** - Images, fonts, static files

**Project Placement Issues:**

```
[List files in wrong layers or incorrect directory structure]
```

#### 6.2 Reuse of Existing Components

**Before Creating New Code - Check for Existing:**

- [ ] **Services**: Scan existing service interfaces for similar functionality
- [ ] **Repositories**: Check for existing data access patterns
- [ ] **DTOs**: Look for existing DTOs that can be reused or extended
- [ ] **Utilities**: Check for helper functions before creating new ones
- [ ] **Base Classes**: Use existing base classes for audit fields, entities, etc.
- [ ] **Error Handling**: Follow existing error logging/exception patterns
- [ ] **Background Jobs**: Check for existing worker/job patterns
- [ ] **Authentication**: Use existing auth infrastructure

**Frontend - Check for Existing:**

- [ ] **Components**: Search for existing UI components before building new ones
- [ ] **Hooks**: Check for existing custom hooks
- [ ] **State Management**: Use existing context/store patterns
- [ ] **Form Patterns**: Follow existing form validation patterns
- [ ] **Layout Components**: Reuse existing layout infrastructure
- [ ] **Utilities**: Check for existing helper functions

**Component Reuse Issues:**

```
[List where implementation creates new components instead of reusing existing]
```

#### 6.3 File Organization

- [ ] All new files listed with full paths
- [ ] Modified files listed separately
- [ ] File naming follows project conventions (PascalCase for C#, camelCase for TS)
- [ ] Folder structure follows established patterns (see 6.1 above)
- [ ] No files in incorrect projects (e.g., DTOs in API project)

**File Organization Issues:**

```
[List any file placement or naming issues]
```

#### 6.4 Implementation Steps

- [ ] Steps are specific and actionable
- [ ] Dependencies between steps clearly identified
- [ ] Migration steps included (database, configuration)
- [ ] Deployment considerations documented
- [ ] Rollback strategy defined

**Step Clarity Issues:**

```
[List any vague or missing steps]
```

---

### 7. Testing Strategy

#### 7.1 Test Coverage

- [ ] Unit test requirements specified
- [ ] Integration test scenarios defined
- [ ] End-to-end test cases identified
- [ ] Test data requirements documented
- [ ] Multi-tenant test scenarios included

**Testing Gaps:**

```
[List any missing test scenarios]
```

#### 7.2 Test Infrastructure

- [ ] Test project organization specified
- [ ] Mock/stub strategy defined
- [ ] Test database setup documented
- [ ] CI/CD integration considered

**Test Infrastructure Issues:**

```
[List any test setup concerns]
```

---

### 8. Dependencies & Integration

#### 8.1 External Dependencies

- [ ] All NuGet packages identified with versions (.NET 9)
- [ ] npm packages identified with versions (frontend - React 18, Material-UI v5)
- [ ] External API dependencies documented
- [ ] Authentication requirements for external services
- [ ] AI service dependencies (OpenAI, Azure OpenAI) for ETL classification

**Dependency Issues:**

```
[List any missing or problematic dependencies]
```

#### 8.2 System Integration

- [ ] Integration points with existing systems identified
- [ ] Data flow between components documented
- [ ] Background job interactions specified (check Workers folder)
- [ ] ETL pipeline integration (Extract, Transform, Load services)
- [ ] AI assistant integration (if using classification features)
- [ ] SignalR hub usage defined (if applicable for real-time features)

**Integration Issues:**

```
[List any integration concerns]
```

---

### 9. Domain-Specific Concerns (Optional)

*Adapt this section to your domain. Examples provided for common scenarios.*

#### 9.1 Compliance Requirements (If Applicable)

*Examples: Healthcare (HIPAA), Finance (PCI-DSS), EU (GDPR), etc.*

- [ ] User roles from spec properly mapped
- [ ] Domain-specific workflows respected
- [ ] Timezone handling for multi-region operations
- [ ] Sensitive data (PII, PHI, PCI) handling compliant
- [ ] Audit trail requirements met

**Compliance Issues:**

```
[List any compliance-specific concerns for your domain]
```

#### 9.2 Multi-Tenancy (If Applicable)

- [ ] Users associated with specific tenants/organizations
- [ ] Data access filtered by user's assigned tenant
- [ ] Tenant context properly set in API calls
- [ ] Cross-tenant access prevented (except for Admin role)

**Tenant Access Issues:**

```
[List any tenant isolation concerns]
```

#### 9.3 AI/ML Integration (If Applicable)

- [ ] AI service integration follows existing patterns
- [ ] Classification/inference audit trail maintained
- [ ] Error handling for AI service failures
- [ ] Rate limiting and cost management considered
- [ ] Fallback behavior when AI service unavailable

**AI Integration Issues:**

```
[List any AI/ML integration concerns]
```

---

### 10. Logic & Correctness

#### 10.1 Business Logic

- [ ] Business rules correctly implemented
- [ ] Edge cases identified and handled
- [ ] Data validation rules comprehensive
- [ ] State transitions properly managed
- [ ] Concurrency scenarios considered

**Logic Errors:**

```
[List any business logic flaws or missing edge cases]
```

#### 10.2 Data Integrity

- [ ] Referential integrity maintained
- [ ] Cascade delete behavior specified
- [ ] Orphan record prevention
- [ ] Data consistency across transactions
- [ ] Idempotency for critical operations

**Data Integrity Issues:**

```
[List any data integrity concerns]
```

---

### 11. Configuration & Deployment

#### 11.1 Configuration Management

- [ ] All configuration in appsettings.json (not hardcoded)
- [ ] Environment-specific settings documented
- [ ] Secrets management strategy (Azure Key Vault, user secrets)
- [ ] Configuration seeding for permissions/roles

**Configuration Issues:**

```
[List any configuration concerns]
```

#### 11.2 Deployment Considerations

- [ ] Database migration strategy
- [ ] Zero-downtime deployment considerations
- [ ] Backward compatibility with existing data
- [ ] Rollback procedure documented

**Deployment Concerns:**

```
[List any deployment issues]
```

---

### 12. Documentation & Communication

#### 12.1 Technical Documentation

- [ ] Architecture diagrams clear and accurate
- [ ] Data flow diagrams show complete workflows
- [ ] API contracts fully documented
- [ ] Code comments strategy defined

**Documentation Gaps:**

```
[List any missing or unclear documentation]
```

#### 12.2 User-Facing Changes

- [ ] UI changes match UX mockups (if provided)
- [ ] User notifications and feedback messages defined
- [ ] Help text and tooltips planned
- [ ] User training needs identified

**User Communication Issues:**

```
[List any user-facing concerns]
```

---

## Critical Issues Summary

### Blockers (Must Fix Before Development)

```
[List critical issues that would cause development failure]
```

### Major Concerns (Should Fix Before Development)

```
[List significant issues that could cause problems]
```

### Minor Issues (Can Address During Development)

```
[List minor improvements or clarifications needed]
```

---

## Recommendations

```
[List suggestions for improvement or alternative approaches]
```

---

## Approval Decision

**Status:** [ ] Approved [ ] Approved with Changes [ ] Rejected

**Approver:** ________________________
**Date:** ________________________

**Notes:**

```
[Additional approval notes]
```

---

## QA Audit Metadata

| Field | Value |
|-------|-------|
| **Audited By** | |
| **Audit Date** | |
| **Audit Duration** | |
| **Business Spec Version** | |
| **Implementation Plan Version** | |

### 
