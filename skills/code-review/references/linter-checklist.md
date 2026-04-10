# Linter Zero-New-Violations Checklist

## Goal

Changed files must not introduce new linter warnings or errors. Existing violations in unchanged code are out of scope.

## Linter Detection

Detect which linters are configured by checking for config files in the project root and common locations:

### JavaScript / TypeScript
| Config file | Linter |
|---|---|
| `.eslintrc`, `.eslintrc.*`, `eslint.config.*` | ESLint |
| `biome.json`, `biome.jsonc` | Biome |
| `.prettierrc`, `.prettierrc.*`, `prettier.config.*` | Prettier (formatter) |
| `deno.json`, `deno.jsonc` | Deno lint |

**Run**: `npx eslint <changed-files>`, `npx biome check <changed-files>`, `npx prettier --check <changed-files>`

### Python
| Config file / section | Linter |
|---|---|
| `pyproject.toml` → `[tool.ruff]` | Ruff |
| `pyproject.toml` → `[tool.flake8]`, `.flake8`, `setup.cfg` | Flake8 |
| `pyproject.toml` → `[tool.pylint]`, `.pylintrc` | Pylint |
| `pyproject.toml` → `[tool.mypy]`, `mypy.ini` | mypy (type checker) |
| `pyrightconfig.json`, `pyproject.toml` → `[tool.pyright]` | Pyright |

**Run**: `ruff check <changed-files>`, `flake8 <changed-files>`, `mypy <changed-files>`

### Go
| Config file | Linter |
|---|---|
| `.golangci.yml`, `.golangci.yaml`, `.golangci.toml` | golangci-lint |

**Run**: `golangci-lint run <changed-packages>`

### Rust
| Indicator | Linter |
|---|---|
| `Cargo.toml` (always available) | `cargo clippy` |

**Run**: `cargo clippy -- -D warnings`

### Ruby
| Config file | Linter |
|---|---|
| `.rubocop.yml` | RuboCop |

**Run**: `rubocop <changed-files>`

### PHP
| Config file | Linter |
|---|---|
| `phpstan.neon`, `phpstan.neon.dist` | PHPStan |
| `.php-cs-fixer.php`, `.php-cs-fixer.dist.php` | PHP-CS-Fixer |

**Run**: `vendor/bin/phpstan analyse <changed-files>`, `vendor/bin/php-cs-fixer fix --dry-run <changed-files>`

### CSS / Styles
| Config file | Linter |
|---|---|
| `.stylelintrc`, `.stylelintrc.*`, `stylelint.config.*` | Stylelint |

**Run**: `npx stylelint <changed-files>`

### Multi-language / Meta
| Config file | Tool |
|---|---|
| `.pre-commit-config.yaml` | pre-commit (runs multiple linters) |
| `Makefile`, `Taskfile.yml` | Check for `lint` target |
| `package.json` → `scripts.lint` | npm/yarn lint script |

## Baseline Comparison Strategy

The core principle: **only flag violations in lines that were changed or added**.

### Step 1: Identify changed files
```bash
git diff --name-only --diff-filter=ACMR HEAD
```
Use `--diff-filter=ACMR` to include Added, Copied, Modified, Renamed files (skip Deleted).

### Step 2: Run linter on changed files only
Most linters accept file arguments. Always prefer targeting changed files over full project lint.

### Step 3: Cross-reference with diff
For each linter violation:
1. Get the file and line number from the linter output.
2. Check if that line is within a changed hunk (`git diff` ranges).
3. Only report violations on changed/added lines.

### Hunk Extraction
```bash
git diff -U0 HEAD -- <file> | grep '^@@' | sed 's/.*+\([0-9]*\),\?\([0-9]*\).*/\1 \2/'
```
This gives `start_line count` pairs for added/modified lines.

## Classification

| Condition | Severity | Action |
|---|---|---|
| New error in changed line | **P1** | Must fix before merge |
| New warning in changed line | **P2** | Should fix in this PR |
| Pre-existing violation in unchanged code | — | Out of scope (do not report) |
| Formatter-only issue (whitespace, trailing comma) | **P3** | Auto-fixable, suggest running formatter |

## Common Auto-fix Commands

| Linter | Auto-fix |
|---|---|
| ESLint | `npx eslint --fix <files>` |
| Biome | `npx biome check --write <files>` |
| Ruff | `ruff check --fix <files>` |
| Prettier | `npx prettier --write <files>` |
| RuboCop | `rubocop -A <files>` |
| golangci-lint | `golangci-lint run --fix <packages>` |
| cargo clippy | `cargo clippy --fix` |
| Stylelint | `npx stylelint --fix <files>` |

## Edge Cases

- **No linter detected**: Skip this step, note in review output that no linter was found.
- **Linter not installed**: Note which linter config was found but could not be executed. Do not fail the review.
- **Monorepo**: Check for linter configs at both root and package/module level. Use the most specific config.
- **CI linter config differs from local**: If a CI config (e.g., `.github/workflows`) runs a different lint command, prefer the CI version as the source of truth.
- **Generated files**: Skip files in common generated paths (`node_modules/`, `dist/`, `build/`, `vendor/`, `*_generated.*`, `*.pb.go`).
