# skill-sync

Public backup of my personal Codex skills.

## Layout

- `skills/`: personal Codex skills intended to be copied into `~/.codex/skills`.

Built-in `.system` skills are intentionally not included because Codex provides them separately.

## Restore

Copy the desired skill folder into your Codex skills directory, then restart Codex.

```powershell
Copy-Item -Path .\skills\ast-grep -Destination "$HOME\.codex\skills\ast-grep" -Recurse
```
