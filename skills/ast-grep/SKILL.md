---
name: ast-grep
description: Use ast-grep (`sg`/`ast-grep`) for structural code search, AST-aware refactors, safe code rewrites, custom lint rules, and large-scale migrations. Trigger when plain text search is too brittle, when matching code syntax across JavaScript, TypeScript, Python, Rust, Go, Java, C/C++, CSS, HTML, YAML, or other tree-sitter-backed languages, or when the user asks to find, replace, scan, verify, or codemod code by syntax shape.
---

# ast-grep

## Quick Start

Prefer ast-grep when the task depends on code structure rather than exact text. Use `rg` first for simple filename or text discovery, then use ast-grep once the target pattern is known.

Check availability:

```bash
sg --help
ast-grep --help
```

If neither command exists, explain that ast-grep is not installed and suggest one of the official install paths:

```bash
npm install --global @ast-grep/cli
pip install ast-grep-cli
cargo install ast-grep --locked
```

## One-off Search

Use `sg run` or the top-level CLI form, depending on the installed version. Always pass a language when the extension is ambiguous.

```bash
sg run --pattern 'console.log($$$ARGS)' --lang ts
ast-grep --pattern 'console.log($$$ARGS)' --lang ts
```

Common flags:

- `--pattern` / `-p`: syntax-shaped pattern to match.
- `--lang` / `-l`: language such as `ts`, `tsx`, `js`, `py`, `rs`, `go`, `java`, `c`, `cpp`, `css`, `html`, or `yaml`.
- `--rewrite` / `-r`: replacement pattern.
- `--interactive` / `-i`: review replacements interactively.

Pattern metavariables use `$NAME` for a single AST node and `$$$NAME` for a variadic capture. Use uppercase names for captures.

## Rewrites

For code-changing tasks, start with a read-only search, then run an interactive rewrite or inspect the diff before saving.

```bash
sg run -p '$A && $A()' -r '$A?.()' -l ts
sg run -p 'console.log($$$ARGS)' -r 'logger.debug($$$ARGS)' -l ts -i
```

After applying changes, run the repo's normal formatter and tests. Treat ast-grep as the structural editing tool, not as proof that the resulting code is semantically correct.

## Project Rules

For repeated checks, codemods, or lint-style rules, create or update `sgconfig.yml` and rule YAML files instead of embedding long commands in shell history.

Use config-driven scans when:

- The rule needs constraints beyond a simple pattern.
- The user wants a reusable lint or migration.
- Multiple rules should run together.
- The result should be reviewed in CI or by future Codex runs.

Read `references/rules.md` before creating a non-trivial rule.

## Workflow

1. Identify representative files and language.
2. Draft the smallest pattern that matches the target syntax.
3. Run ast-grep read-only and inspect matches.
4. Add constraints or a YAML rule if the pattern overmatches.
5. For rewrites, apply interactively or review diffs before finalizing.
6. Run formatting, tests, or type checks that are appropriate for the repo.

## Safety

Avoid broad rewrites from the repository root until the pattern has been tested on representative files. Use path filters or run from a narrower directory when possible. Never assume textual similarity implies structural equivalence; validate with actual matches.
