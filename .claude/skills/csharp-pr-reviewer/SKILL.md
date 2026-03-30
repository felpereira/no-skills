---
name: csharp-pr-reviewer
description: Reviews C# .NET code changes in the current branch (relative to develop) with deep analysis, reading changed files via git diff and producing a formatted report with only medium and critical findings. Use this skill whenever the user asks for code review, PR analysis, quality check, or wants to find problems in C# .NET code — even if they just say "revisa o código", "analisa o código", or "o que tem de errado aqui".
---

# C# .NET PR Code Reviewer

## Dependencies
- Git repository with a `develop` branch as base

## Workflow

### 1. Detect current branch and changed files

Get the list of files changed in the current branch relative to `develop`:

```bash
git diff --name-only --diff-filter=ACMR develop
```

Also get the full diff for reading the actual code changes:

```bash
git diff --diff-filter=ACMR develop
```

To get the branch name for the report header:

```bash
git rev-parse --abbrev-ref HEAD
```

Only analyze `.cs` files from the diff output. Skip:
- Migration files (`**/Migrations/*.cs`)
- Auto-generated files (`*.Designer.cs`, `*.g.cs`, `*.generated.cs`)
- `AssemblyInfo.cs`, `GlobalUsings.cs`

For each changed `.cs` file, also read its full current content from disk using the Read tool to have complete context beyond just the diff lines.

### 2. Look for a custom rules file

Search for a rules file in this order:
1. `REVIEW_RULES.md` in the current directory
2. `docs/REVIEW_RULES.md`
3. `.claude/REVIEW_RULES.md`

If found, read it and incorporate those rules into the analysis — they take priority over the defaults below. Mention at the top of the report which rules file was used.

### 3. Deep Analysis

For each modified `.cs` file, analyze across all categories below. Only surface **Medium** and **Critical** findings — ignore minor style issues, low-impact suggestions, or purely cosmetic problems.

---

#### Security
- Raw SQL without parameterization (SQL injection risk)
- Secrets, tokens, or passwords hardcoded or logged
- Missing authorization checks on controllers/endpoints (`[Authorize]`, policy checks)
- Insecure deserialization
- Mass assignment risks (missing DTOs, `[BindNever]`, or over-posting vulnerabilities)
- Missing or insufficient input validation

#### Performance
- N+1 queries: loading related data inside loops without `.Include()` or batch loading
- Missing `.AsNoTracking()` on read-only EF queries
- Blocking async code: `.Result`, `.Wait()`, `.GetAwaiter().GetResult()` in async paths
- Loading large datasets without pagination
- Inefficient LINQ chains that translate to poor SQL (e.g., `ToList()` before `Where()`)
- Unnecessary repeated database calls that could be batched or cached

#### Code Quality & Best Practices
- SOLID violations: God classes, SRP violations, tight coupling, missing abstractions
- Swallowed exceptions: empty `catch` blocks or catching `Exception` without logging/rethrowing
- Magic numbers or magic strings that should be constants or configuration values
- Nullable reference type risks: missing null checks, improper use of `!` operator
- Missing `CancellationToken` propagation in async methods that accept them
- Dead code, commented-out blocks left in production code

#### MediatR Patterns
- Business logic leaking out of handlers (into controllers, middleware, etc.)
- Commands/queries missing FluentValidation validators when a validation pipeline is in use
- Handlers doing too much (God handlers violating SRP)
- Inconsistent or missing return types on handlers

#### Entity Framework Patterns
- `DbContext` injected directly into classes that shouldn't own it (controllers, domain services)
- Lazy loading pitfalls: missing `.Include()` leading to unintended queries after context disposal
- Multi-step operations missing explicit transaction scope (`IDbContextTransaction`)
- Modifying tracked entities in unexpected places, causing unintended saves
- Using EF with `Select(*)` for heavy reporting — should use Dapper or raw SQL projections

#### Architecture & Layering
- Dependency direction violations (e.g., Domain layer referencing Infrastructure or Application)
- Infrastructure concerns leaking into Application or Domain layers (EF types, HTTP clients, etc.)
- Concrete class usage where interfaces should be used (breaks testability and DI)
- Domain logic placed in Application layer or vice versa
- Anti-patterns for the architecture in use (Clean Architecture, DDD, etc.)

---

### 4. Severity classification

**Critical** — Will cause bugs, security vulnerabilities, data loss, or serious production performance problems. Must be fixed before merge.

**Medium** — Violates important best practices, creates significant technical debt, or will likely become a critical issue as the codebase grows. Should be addressed before or shortly after merge.

Do not include Low or Info level findings.

### 5. Generate the report

Use this exact format:

---

## Code Review Report — Branch `<branch-name>`

**Base branch:** `develop`
**Files analyzed:** N `.cs` files
**Issues found:** X critical, Y medium
_(Custom rules applied from: `path/to/REVIEW_RULES.md`)_ ← include only if a rules file was found

---

### 🔴 Critical Issues

#### `src/Path/To/File.cs`

**[CRITICAL] Short descriptive title of the issue**

> **Line(s):** 42–48
> **Category:** Security | Performance | Code Quality | MediatR | EF | Architecture
> **Problem:** Clear explanation of what is wrong and why it matters in production.
> **Recommendation:** Specific fix guidance. Include a brief code example if it makes the fix clearer.

_(repeat for each critical issue, grouped by file)_

---

### 🟡 Medium Issues

#### `src/Path/To/AnotherFile.cs`

**[MEDIUM] Short descriptive title**

> **Line(s):** 15
> **Category:** ...
> **Problem:** ...
> **Recommendation:** ...

_(repeat for each medium issue, grouped by file)_

---

### ✅ Summary

| Category       | Critical | Medium |
|----------------|----------|--------|
| Security       |          |        |
| Performance    |          |        |
| Code Quality   |          |        |
| MediatR        |          |        |
| EF             |          |        |
| Architecture   |          |        |
| **Total**      | **X**    | **Y**  |

**Top priorities before merging:**
1. (most impactful critical item)
2. ...

---

If no medium or critical issues are found, say so clearly and briefly note what was checked.
