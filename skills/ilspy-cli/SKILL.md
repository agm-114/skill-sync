---
name: ilspy-cli
description: Use ILSpy CLI (`ilspycmd`) to inspect, decompile, or extract information from .NET assemblies such as `.dll`, `.exe`, NuGet/package assemblies, and WPF resources. Trigger when the user asks to decompile C#, list types, inspect IL, extract embedded resources/BAML/XAML, generate a compilable project, or understand a compiled .NET binary.
---

# ILSpy CLI

## Quick Start

Check availability first:

```powershell
ilspycmd --version
```

If `ilspycmd` is missing, explain that ILSpy CLI is not installed and install it as a global .NET tool:

```powershell
dotnet tool install --global ilspycmd
```

## Common Tasks

Decompile an assembly to stdout for quick inspection:

```powershell
ilspycmd path\to\assembly.dll
```

List types before choosing a target:

```powershell
ilspycmd path\to\assembly.dll --list c,i,s,d,e
```

Decompile a single fully qualified type:

```powershell
ilspycmd path\to\assembly.dll --type Namespace.TypeName
```

Generate a compilable project into a dedicated output directory:

```powershell
ilspycmd path\to\assembly.dll --project --outputdir .\decompiled
```

Inspect IL instead of reconstructed C#:

```powershell
ilspycmd path\to\assembly.dll --ilcode
```

List or extract embedded resources:

```powershell
ilspycmd path\to\assembly.dll --list-resources
ilspycmd path\to\assembly.dll --resource resource.name --outputdir .\resources
```

For WPF assemblies, use BAML decompilation when the task is about XAML UI:

```powershell
ilspycmd path\to\assembly.dll --project --decompile-baml --outputdir .\decompiled
```

## Workflow

1. Identify the target assembly and any nearby `.pdb`, `.deps.json`, or dependency folders.
2. Run `--list` or `--list-resources` first when the user asks about a specific class, API surface, or resource.
3. Prefer `--type` for focused questions and `--project --outputdir` for broader analysis or navigation.
4. Use `--referencepath <directory>` when missing dependencies affect decompilation quality.
5. Keep generated output in a clearly named directory, then cite decompiled file paths or command output in the answer.

## Safety

Treat decompiled output as an approximation of the original source. Preserve legal and licensing context, avoid claiming it is exact source code, and do not decompile third-party binaries for prohibited purposes. Do not overwrite existing source trees with decompiler output.
