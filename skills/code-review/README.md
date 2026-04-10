# Code Review

A comprehensive code review skill combining **Clean Code** principles (Robert C. Martin) with **senior engineer** review expertise. Performs structured reviews covering architecture, SOLID, security, performance, clean code, and quality.

## Installation

```bash
npx skills add caoergou/erics-skills --skill code-review
```

## Features

- **Clean Code Principles** - Meaningful names, small functions, comment hygiene, formatting
- **SOLID Principles** - Detect SRP, OCP, LSP, ISP, DIP violations
- **Security Scan** - XSS, injection, SSRF, race conditions, auth gaps, secrets leakage
- **Performance** - N+1 queries, CPU hotspots, missing cache, memory issues
- **Error Handling** - Swallowed exceptions, async errors, missing boundaries
- **Boundary Conditions** - Null handling, empty collections, off-by-one, numeric limits
- **Removal Planning** - Identify dead code with safe deletion plans

## Usage

After installation, simply run:

```
/code-review
```

The skill will automatically review your current git changes.

## Workflow

1. **Preflight** - Scope changes via `git diff`
2. **Clean Code** - Check naming, function size, comments, formatting
3. **SOLID + Architecture** - Check design principles
4. **Removal Candidates** - Find dead/unused code
5. **Security Scan** - Vulnerability detection
6. **Code Quality** - Error handling, performance, boundaries
7. **Output** - Findings by severity (P0-P3)
8. **Confirmation** - Ask user before implementing fixes

## Severity Levels

| Level | Name | Action |
|-------|------|--------|
| P0 | Critical | Must block merge |
| P1 | High | Should fix before merge |
| P2 | Medium | Fix or create follow-up |
| P3 | Low | Optional improvement |

## Structure

```
code-review/
├── SKILL.md                     # Main skill definition
├── README.md                    # This file
├── agents/
│   └── agent.yaml               # Agent interface config
└── references/
    ├── solid-checklist.md       # SOLID smell prompts + common code smells
    ├── security-checklist.md    # Security & reliability
    ├── code-quality-checklist.md # Error, perf, boundaries
    └── removal-plan.md          # Deletion planning template
```

## License

MIT
