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

### 2. Look for custom rules

Search for rules using this priority order:

1. **`rules/` subdirectory** — look for this folder at the project root, then `docs/rules/`, then `.claude/rules/`. If found, read **all `.md` files** inside it (sorted by filename). Merge their contents as the active ruleset.
2. **Single rules file fallback** — if no `rules/` directory is found, search for `REVIEW_RULES.md` in the current directory, then `docs/REVIEW_RULES.md`, then `.claude/REVIEW_RULES.md`.

If any rules source is found, incorporate those rules into the analysis — they take priority over the defaults below. Mention at the top of the report which source was used (e.g., `rules/ (11 files)` or `REVIEW_RULES.md`).

### 3. Deep Analysis

For each modified `.cs` file, apply every rule from the ruleset loaded in Step 2. Only surface **Medium** and **Critical** findings — ignore minor style issues, low-impact suggestions, or purely cosmetic problems.

Analyze across these categories (defined in full in the loaded rules files):

- **Security** — SQL injection, hardcoded secrets, missing authorization, insecure deserialization, mass assignment
- **Performance** — N+1 queries, missing AsNoTracking, blocking async, pagination, inefficient LINQ
- **Code Quality** — SOLID violations, swallowed exceptions, magic numbers, nullable risks, dead code
- **MediatR** — business logic in controllers, missing validators, God handlers, inconsistent return types
- **Entity Framework** — DbContext exposure, lazy loading, missing transactions, tracked entity mutations, reporting queries
- **Architecture** — dependency direction, infra leaking into domain, concrete vs interface
- **Organization** — new files that are not referenced anywhere

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

| Category     | Critical | Medium |
| ------------ | -------- | ------ |
| Security     |          |        |
| Performance  |          |        |
| Code Quality |          |        |
| MediatR      |          |        |
| EF           |          |        |
| Architecture |          |        |
| Organization |          |        |
| **Total**    | **X**    | **Y**  |

**Top priorities before merging:**

1. (most impactful critical item)
2. ...

---

If no medium or critical issues are found, say so clearly and briefly note what was checked.

After the summary, always display this block:

---

💡 **Quer que eu corrija os problemas encontrados?**
Diga "corrija os erros" ou "aplica as correções" e farei as alterações diretamente nos arquivos, seguindo as regras do `REVIEW_RULES.md`.

---

### 6. Apply fixes (only when the user explicitly requests it)

When the user asks to fix the errors (e.g., "corrija os erros", "aplica as correções", "corrige aí"):

1. **Re-read `REVIEW_RULES.md`** before making any changes to ensure every fix is compliant with the project rules.

2. **Process issues in priority order**: Critical first, then Medium.

3. **For each fixable issue**, use the `Edit` tool to apply the minimal change that resolves the problem. Rules:
   - Only change the lines related to the reported issue
   - Do NOT refactor surrounding code that was not flagged
   - Do NOT add features, improve structure, or make "nice to have" improvements
   - Do NOT use the `Write` tool to rewrite whole files — always use `Edit`
   - Every fix must comply with the rules in `REVIEW_RULES.md`

4. **Issues that require manual intervention** (do not fix automatically):
   - Moving classes between architecture layers
   - Significant refactors that affect multiple files beyond the diff
   - Changes requiring product/domain decisions
   - Ambiguous issues where multiple valid approaches exist

5. **After all files are processed**, output a report using this exact format:

---

## Correções Aplicadas

### `src/Path/To/File.cs`

- **[CRITICAL]** Título do issue → o que foi corrigido
- **[MEDIUM]** Título do issue → o que foi corrigido

### `src/Path/To/OutroFile.cs`

- ...

**Issues não corrigidos automaticamente** (requerem revisão manual):

- `arquivo.cs` linha X — [motivo pelo qual não foi corrigido automaticamente]

---

If all issues were fixed automatically, omit the "Issues não corrigidos" section.
If no issues were fixable automatically, state that clearly and list them all under "Issues não corrigidos automaticamente".
