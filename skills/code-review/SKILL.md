---
name: code-review
description: "Comprehensive code review combining Clean Code principles and senior engineer expertise. Reviews git changes for SOLID violations, security risks, clean code smells, and proposes actionable improvements."
---

# Code Review

Comprehensive code review skill that combines **Clean Code** principles (Robert C. Martin) with **senior engineer expertise** for SOLID, architecture, security, performance, and code quality.

## When to Use

- **Reviewing Pull Requests**: Provide constructive, principle-based feedback.
- **Writing new code**: Ensure high quality from the start.
- **Refactoring legacy code**: Identify and remove code smells.
- **Improving team standards**: Align on industry-standard best practices.

## Severity Levels

| Level | Name | Description | Action |
|-------|------|-------------|--------|
| **P0** | Critical | Security vulnerability, data loss risk, correctness bug | Must block merge |
| **P1** | High | Logic error, significant SOLID violation, performance regression | Should fix before merge |
| **P2** | Medium | Code smell, maintainability concern, minor SOLID violation | Fix in this PR or create follow-up |
| **P3** | Low | Style, naming, minor suggestion | Optional improvement |

## Workflow

### 1) Preflight Context

- Use `git status -sb`, `git diff --stat`, and `git diff` to scope changes.
- If needed, use `rg` or `grep` to find related modules, usages, and contracts.
- Identify entry points, ownership boundaries, and critical paths (auth, payments, data writes, network).

**Edge cases:**
- **No changes**: If `git diff` is empty, inform user and ask if they want to review staged changes or a specific commit range.
- **Large diff (>500 lines)**: Summarize by file first, then review in batches by module/feature area.
- **Mixed concerns**: Group findings by logical feature, not just file order.

### 2) Clean Code Principles

Apply the following Clean Code principles during review:

#### Meaningful Names
- **Intention-Revealing Names**: `elapsedTimeInDays` instead of `d`.
- **Avoid Disinformation**: Don't use `accountList` if it's actually a `Map`.
- **Meaningful Distinctions**: Avoid `ProductData` vs `ProductInfo`.
- **Pronounceable/Searchable Names**: Avoid `genymdhms`.
- **Class Names**: Nouns (`Customer`, `WikiPage`). Avoid `Manager`, `Data`.
- **Method Names**: Verbs (`postPayment`, `deletePage`).

#### Functions
- **Small**: Functions should be shorter than you think (~20 lines max).
- **Do One Thing**: A function should do only one thing, and do it well.
- **One Level of Abstraction**: Don't mix high-level business logic with low-level details.
- **Descriptive Names**: `isPasswordValid` is better than `check`.
- **Arguments**: 0 is ideal, 1-2 is okay, 3+ requires strong justification.
- **No Side Effects**: Functions shouldn't secretly change global state.

#### Comments
- **Don't Comment Bad Code — Rewrite It**: Most comments are a sign of failure to express ourselves in code.
- **Good Comments**: Legal, Informative (regex intent), Clarification (external libraries), TODOs.
- **Bad Comments**: Mumbling, Redundant, Misleading, Mandated, Noise, Position Markers.

#### Formatting
- **The Newspaper Metaphor**: High-level concepts at the top, details at the bottom.
- **Vertical Density**: Related lines should be close to each other.
- **Distance**: Variables declared near their usage.

#### Objects, Data Structures & Error Handling
- **Data Abstraction**: Hide implementation behind interfaces.
- **Law of Demeter**: Avoid `a.getB().getC().doSomething()`.
- **Use Exceptions instead of Return Codes**.
- **Don't Return/Pass Null**.

### 3) SOLID + Architecture Smells

- Load `references/solid-checklist.md` for specific prompts.
- Look for:
  - **SRP**: Overloaded modules with unrelated responsibilities.
  - **OCP**: Frequent edits to add behavior instead of extension points.
  - **LSP**: Subclasses that break expectations or require type checks.
  - **ISP**: Wide interfaces with unused methods.
  - **DIP**: High-level logic tied to low-level implementations.
- When you propose a refactor, explain *why* it improves cohesion/coupling and outline a minimal, safe split.
- If refactor is non-trivial, propose an incremental plan instead of a large rewrite.

### 4) Removal Candidates + Iteration Plan

- Load `references/removal-plan.md` for template.
- Identify code that is unused, redundant, or feature-flagged off.
- Distinguish **safe delete now** vs **defer with plan**.
- Provide a follow-up plan with concrete steps and checkpoints (tests/metrics).

### 5) Security and Reliability Scan

- Load `references/security-checklist.md` for coverage.
- Check for:
  - XSS, injection (SQL/NoSQL/command), SSRF, path traversal
  - AuthZ/AuthN gaps, missing tenancy checks
  - Secret leakage or API keys in logs/env/files
  - Rate limits, unbounded loops, CPU/memory hotspots
  - Unsafe deserialization, weak crypto, insecure defaults
  - **Race conditions**: concurrent access, check-then-act, TOCTOU, missing locks
- Call out both **exploitability** and **impact**.

### 6) Code Quality Scan

- Load `references/code-quality-checklist.md` for coverage.
- Check for:
  - **Error handling**: swallowed exceptions, overly broad catch, missing error handling, async errors
  - **Performance**: N+1 queries, CPU-intensive ops in hot paths, missing cache, unbounded memory
  - **Boundary conditions**: null/undefined handling, empty collections, numeric boundaries, off-by-one
- Flag issues that may cause silent failures or production incidents.

### 7) Output Format

Structure your review as follows:

```markdown
## Code Review Summary

**Files reviewed**: X files, Y lines changed
**Overall assessment**: [APPROVE / REQUEST_CHANGES / COMMENT]

---

## Findings

### P0 - Critical
(none or list)

### P1 - High
1. **[file:line]** Brief title
  - Description of issue
  - Suggested fix

### P2 - Medium
2. (continue numbering across sections)
  - ...

### P3 - Low
...

---

## Clean Code Issues
(naming, function size, comment quality, formatting — grouped by principle)

## Removal/Iteration Plan
(if applicable)

## Additional Suggestions
(optional improvements, not blocking)
```

**Inline comments**: Use this format for file-specific findings:
```
::code-comment{file="path/to/file.ts" line="42" severity="P1"}
Description of the issue and suggested fix.
::
```

**Clean review**: If no issues found, explicitly state:
- What was checked
- Any areas not covered (e.g., "Did not verify database migrations")
- Residual risks or recommended follow-up tests

### 8) Next Steps Confirmation

After presenting findings, ask user how to proceed:

```markdown
---

## Next Steps

I found X issues (P0: _, P1: _, P2: _, P3: _).

**How would you like to proceed?**

1. **Fix all** - I'll implement all suggested fixes
2. **Fix P0/P1 only** - Address critical and high priority issues
3. **Fix specific items** - Tell me which issues to fix
4. **No changes** - Review complete, no implementation needed

Please choose an option or provide specific instructions.
```

**Important**: Do NOT implement any changes until user explicitly confirms. This is a review-first workflow.

## Clean Code Checklist

- [ ] Is this function smaller than 20 lines?
- [ ] Does this function do exactly one thing?
- [ ] Are all names searchable and intention-revealing?
- [ ] Have I avoided comments by making the code clearer?
- [ ] Am I passing too many arguments?
- [ ] Is there a failing test for this change?
- [ ] Does the code follow the Law of Demeter?
- [ ] Are exceptions used instead of return codes?

## Resources

### references/

| File | Purpose |
|------|---------|
| `solid-checklist.md` | SOLID smell prompts, common code smells, and refactor heuristics |
| `security-checklist.md` | Web/app security and runtime risk checklist |
| `code-quality-checklist.md` | Error handling, performance, boundary conditions |
| `removal-plan.md` | Template for deletion candidates and follow-up plan |
