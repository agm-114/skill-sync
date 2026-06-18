# ast-grep Rule Reference

Use YAML rules for reusable scans and codemods. Keep simple one-off searches in CLI commands; move to YAML when a rule needs constraints, relational matching, fix text, severity, or repeat use.

## Minimal Rule

```yaml
id: no-console-log
language: TypeScript
rule:
  pattern: console.log($$$ARGS)
message: Avoid console.log in committed TypeScript.
severity: warning
```

Run configured rules with:

```bash
sg scan
```

## Rewrite Rule

```yaml
id: console-to-logger
language: TypeScript
rule:
  pattern: console.log($$$ARGS)
fix: logger.debug($$$ARGS)
message: Replace console.log with logger.debug.
severity: info
```

Use interactive review when applying fixes broadly.

## Constraints

When a plain pattern overmatches, add constraints rather than switching back to brittle text matching. Typical refinements include:

- Limit captures by nested pattern.
- Add relational checks such as inside/has/precedes/follows when supported by the installed ast-grep version.
- Split a large rule into multiple smaller rules when matching several syntax forms.

## Config Habits

Place project rules where the repository already keeps ast-grep config if present. If no config exists, prefer adding a narrowly named rule file and a minimal `sgconfig.yml` only when the user wants a reusable rule. For one-off migrations, keep generated rule files temporary unless the user asks to keep them.

Always validate rules against representative fixtures or real files before applying fixes across the repo.
