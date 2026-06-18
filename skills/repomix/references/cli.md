# Repomix CLI Reference

## Installation And Execution

Repomix can be run without installation:

```powershell
npx repomix@latest
```

Repeated-use options include:

```powershell
npm install -g repomix
yarn global add repomix
bun add -g repomix
brew install repomix
```

It can also run through Docker:

```powershell
docker run -v .:/app -it --rm ghcr.io/yamadashy/repomix
```

## Common Commands

```powershell
repomix
repomix path\to\directory
repomix -o context.xml --style xml
repomix -o context.md --style markdown
repomix -o context.json --style json
repomix -o context.txt --style plain
```

## File Selection

```powershell
repomix --include "src/**/*.ts,**/*.md"
repomix --ignore "**/*.log,tmp/"
repomix --no-gitignore
repomix --no-default-patterns
```

Use `--stdin` to pass file paths from another command:

```powershell
git ls-files "*.ts" | repomix --stdin
rg --files --type ts | repomix --stdin
```

## Output Controls

```powershell
repomix --stdout
repomix --copy
repomix --compress
repomix --remove-comments
repomix --remove-empty-lines
repomix --output-show-line-numbers
repomix --split-output 1mb
```

Use `--parsable-style` when source content may break the chosen output format.

## Token Controls

```powershell
repomix --token-count-tree
repomix --token-count-tree 1000
repomix --token-budget 120000
repomix --token-count-encoding o200k_base
```

`--token-budget` still generates output, but exits non-zero if the packed content exceeds the budget.

## Git Context

```powershell
repomix --include-diffs
repomix --include-logs
repomix --include-logs --include-logs-count 10
repomix --include-diffs --include-logs
```

Use these when the task is about current changes, review context, recent patterns, or a regression.

## Remote Repositories

```powershell
repomix --remote user/repo
repomix --remote https://github.com/user/repo
repomix --remote user/repo --remote-branch main
repomix --remote user/repo --remote-branch 935b695
```

Remote configuration is not trusted by default. Use `--remote-trust-config` only for trusted repositories.

## Configuration

Create a local config:

```powershell
repomix --init
```

Example `repomix.config.json`:

```json
{
  "output": {
    "style": "markdown",
    "filePath": "custom-output.md",
    "removeComments": true,
    "showLineNumbers": true,
    "topFilesLength": 10
  },
  "ignore": {
    "customPatterns": ["*.test.ts", "docs/**"]
  }
}
```

Use `-c, --config <path>` to select a different config. Use `--global` with `--init` for a home-level config.

## Security

Security checks are enabled by default and look for API keys, access tokens, credentials, private keys, environment-variable secrets, AWS credentials, database connection strings, and authentication tokens.

Repomix also excludes binary file contents while keeping binary paths visible in the directory structure.

Disable checks only with explicit user intent:

```powershell
repomix --no-security-check
```

## Agent Skill Generation

Generate an agent skill reference from local or remote code:

```powershell
repomix --skill-generate
repomix --skill-generate custom-name
repomix --skill-output path\to\skill-dir --skill-generate custom-name
repomix --remote user/repo --skill-generate repo-reference
```

Standard include, ignore, compression, comment removal, and remote options also apply to skill generation:

```powershell
repomix --skill-generate --include "src/**/*.ts" --ignore "**/*.test.ts"
repomix --skill-generate --compress
repomix --remote user/repo --skill-generate --compress
```

`--skill-generate` cannot be combined with `--stdout` or `--copy` because it writes a directory.
