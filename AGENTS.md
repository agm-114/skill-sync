# Agent Guide

This repository is a public backup of personal Codex skills.

Use it as a portable source of truth for skills that should survive machine changes,
Codex reinstalls, or local config cleanup. It is not meant to mirror every file under
`.codex/skills`; built-in `.system` skills are provided by Codex and should stay out of
this repo.

## Repository Layout

- `skills/`: personal Codex skill folders.
- `skills/<skill-name>/SKILL.md`: the main instruction file for a skill.
- `skills/<skill-name>/references/`: optional supporting instructions loaded only when
  the skill needs more detail.
- `skills/<skill-name>/agents/`: optional agent metadata for the skill.

## What To Back Up

Back up skills that were created or customized locally.

Do include:

- personal skills under `$HOME\.codex\skills`
- custom `SKILL.md` files
- references, scripts, examples, or metadata required by those skills

Do not include:

- `$HOME\.codex\skills\.system`
- generated caches
- secrets, tokens, credentials, private URLs, or local machine-specific paths unless
  they are necessary and safe to publish

## Skill Sources

- `skills/repomix-explorer`: sourced from the official Repomix repository,
  `yamadashy/repomix`, path `skills/repomix-explorer`. Install/update with:
  `npx skills add yamadashy/repomix --skill repomix-explorer --agent codex`.
  Reference docs: https://repomix.com/guide/repomix-explorer-skill.

## Sync From Local Codex Skills

From the repo root, copy a personal skill into `skills/`:

```powershell
Copy-Item -Path "$HOME\.codex\skills\ast-grep" -Destination ".\skills\ast-grep" -Recurse -Force
```

After copying, inspect the diff before committing:

```powershell
git status --short
git diff
```

## Restore A Skill

Copy the desired backed-up skill into the Codex skills directory, then restart Codex:

```powershell
Copy-Item -Path ".\skills\ast-grep" -Destination "$HOME\.codex\skills\ast-grep" -Recurse -Force
```

For a different skill, replace `ast-grep` with the skill folder name.

## Agent Workflow

When updating this repo:

1. Read `README.md` and this file first.
2. Inspect the target skill folder before editing or syncing it.
3. Preserve the standard Codex skill shape: a concise `SKILL.md` with optional
   progressive-disclosure references.
4. Keep public safety in mind. Remove secrets and machine-specific clutter before
   committing.
5. Prefer small, reviewable changes. This repo is a backup, so avoid unrelated
   restructuring unless the user asks for it.

When using a skill from this repo:

1. Copy it into `$HOME\.codex\skills`.
2. Restart Codex so the skill registry refreshes.
3. Trigger the skill by name or by making a request that matches its description.
