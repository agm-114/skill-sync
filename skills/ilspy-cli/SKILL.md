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


## Mono.Cecil For Targeted Inspection

Use Mono.Cecil when the task needs precise metadata or IL-level queries across an assembly, especially when a custom scan is faster than reading decompiled C#.

Prefer Mono.Cecil for:

- Finding types, fields, properties, events, methods, nested types, and interface implementations programmatically.
- Searching all method bodies for calls to a specific method/property, field access, string literal, enum constant, or opcode pattern.
- Comparing multiple types with the same interface contract.
- Inspecting explicit interface implementation method names such as `Game.UI.IBoardPiece.get_OverrideColor`.
- Finding hidden/internal/private nested types that are awkward to reference from source.
- Checking exact IL control flow when reconstructed C# is ambiguous.

Prefer `ilspycmd` for:

- Reading reconstructed C# for a class or method.
- Producing a navigable decompiled project.
- Understanding broad behavior before writing a focused scan.

A good workflow is: use `ilspycmd --list` or `ilspycmd --type` to orient, then use Mono.Cecil for exact cross-assembly searches.

### Mono.Cecil Scratch Project

If the target repo already references Mono.Cecil, use that. Otherwise create a temporary scratch project outside the source tree and add Mono.Cecil:

```powershell
$dir = Join-Path $env:TEMP 'assembly-cecil-inspect'
New-Item -ItemType Directory -Force -Path $dir | Out-Null
dotnet new console --force --output $dir
dotnet add $dir package Mono.Cecil
```

Then replace `Program.cs` with a focused scanner and run it:

```powershell
dotnet run --project $dir\assembly-cecil-inspect.csproj
```

### Useful Cecil Scanner Template

```csharp
using Mono.Cecil;

var assemblyPath = @"path\to\target.dll";
var asm = AssemblyDefinition.ReadAssembly(assemblyPath);

foreach (var type in asm.MainModule.Types.SelectMany(AllTypes).OrderBy(t => t.FullName))
{
    foreach (var method in type.Methods.Where(m => m.HasBody))
    {
        bool interesting = method.Body.Instructions.Any(i =>
            i.Operand?.ToString()?.Contains("SearchText", StringComparison.OrdinalIgnoreCase) == true);

        if (!interesting)
        {
            continue;
        }

        Console.WriteLine($"==== {type.FullName}::{method.Name} ====");
        foreach (var instruction in method.Body.Instructions)
        {
            Console.WriteLine($"  {instruction.Offset:X4}: {instruction.OpCode} {instruction.Operand}");
        }
    }
}

static IEnumerable<TypeDefinition> AllTypes(TypeDefinition type)
{
    yield return type;
    foreach (var nested in type.NestedTypes.SelectMany(AllTypes))
    {
        yield return nested;
    }
}
```

### Common Cecil Queries

List fields/properties/methods for a type:

```csharp
var type = asm.MainModule.GetType("Namespace.TypeName");
foreach (var field in type.Fields) Console.WriteLine($"FIELD {field.Attributes} {field.FieldType.FullName} {field.Name}");
foreach (var property in type.Properties) Console.WriteLine($"PROPERTY {property.PropertyType.FullName} {property.Name} get={property.GetMethod?.FullName}");
foreach (var method in type.Methods) Console.WriteLine($"METHOD {method.FullName}");
```

Find calls to a method/property by full-name substring:

```csharp
foreach (var type in asm.MainModule.Types.SelectMany(AllTypes))
foreach (var method in type.Methods.Where(m => m.HasBody))
{
    if (method.Body.Instructions.Any(i => i.Operand is MethodReference mr && mr.FullName.Contains("Utility.GameColors::get_Purple")))
    {
        Console.WriteLine($"{type.FullName}::{method.Name}");
    }
}
```

Find string literals:

```csharp
foreach (var type in asm.MainModule.Types.SelectMany(AllTypes))
foreach (var method in type.Methods.Where(m => m.HasBody))
{
    if (method.Body.Instructions.Any(i => i.OpCode.Code == Mono.Cecil.Cil.Code.Ldstr && (string)i.Operand == "UI_HUD_ELINT"))
    {
        Console.WriteLine($"{type.FullName}::{method.Name}");
    }
}
```

### Cecil Safety Notes

Cecil reports exact metadata and IL, but it does not explain high-level intent by itself. When answering, distinguish between exact facts from metadata/IL and inferences about behavior. Prefer `ilspycmd --type` when the user needs readable C# context, and do not overwrite source trees with generated or scratch inspection output.

## Safety

Treat decompiled output as an approximation of the original source. Preserve legal and licensing context, avoid claiming it is exact source code, and do not decompile third-party binaries for prohibited purposes. Do not overwrite existing source trees with decompiler output.

