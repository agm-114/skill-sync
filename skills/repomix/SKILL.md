---
name: repomix
description: Use Repomix to pack local or remote repositories into AI-friendly XML, Markdown, JSON, or plain text context. Trigger when Codex needs to create codebase context bundles for AI review, implementation planning, bug investigation, onboarding, documentation, security review, token budgeting, compressed repository summaries, or Repomix-generated agent skills.
---

# Repomix

Repomix packages repository contents into a single AI-ready artifact. Use it when a task benefits from sharing broad codebase context with another model, archiving a repo snapshot for analysis, or preparing a focused bundle of selected files.

## Quick Start

Prefer running Repomix from the repository root.

```powershell
npx repomix@latest
```

This writes `repomix-output.xml` by default. Use a named output when the artifact should be kept:

```powershell
npx repomix@latest -o context.md --style markdown
```

Use an installed CLI as `repomix` when available; otherwise use `npx repomix@latest`.

## Choose Scope

Pack the whole repository for broad review:

```powershell
repomix
```

Pack a subdirectory:

```powershell
repomix path\to\directory
```

Pack selected files with glob patterns:

```powershell
repomix --include "src/**/*.ts,**/*.md"
```

Exclude noisy files:

```powershell
repomix --ignore "**/*.log,tmp/"
```

Use `--stdin` when another command has already selected the exact file list.

## Optimize Context

Use XML for the default most-structured output, Markdown for human reading, JSON for automation, and plain text only when structure does not matter.

For large repositories:

- Add `--compress` to keep signatures and structure while reducing implementation detail.
- Add `--token-count-tree` to find token-heavy areas before packing.
- Add `--token-budget <number>` in scripts or CI when exceeding context should fail the command.
- Add `--split-output 1mb` or another size when the destination has upload limits.

## Include Git Context

Add recent work context when the AI needs to understand changes, not just current files:

```powershell
repomix --include-diffs --include-logs --include-logs-count 10
```

Use this for code review, regression investigation, PR summaries, and refactor planning.

## Remote Repositories

Repomix can pack GitHub repositories without manually cloning:

```powershell
repomix --remote user/repo
repomix --remote https://github.com/user/repo --remote-branch main
```

Do not trust remote config files unless the user explicitly wants that behavior and the repository is trusted.

## Safety

Keep Repomix security checks enabled by default. They use Secretlint-style checks to catch common secrets and credentials before output is generated.

Only use `--no-security-check` after warning the user that the packed output may expose secrets.

Before sharing a generated bundle publicly, inspect it for secrets, private URLs, customer data, local paths, and irrelevant large files.

## Agent Skill Generation

Use Repomix's `--skill-generate` mode when the goal is a reference skill derived from a codebase:

```powershell
repomix --skill-generate project-reference
repomix --remote user/repo --skill-generate repo-reference
```

This is for codebase reference skills. For hand-authored Codex skills, use the Codex `skill-creator` workflow instead.

## References

Read `references/cli.md` when you need detailed option selection, configuration examples, security behavior, or generated-skill notes.
