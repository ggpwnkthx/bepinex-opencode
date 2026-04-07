---
name: il2cpp-recon
description: Reverse engineer Unity IL2CPP games for modding. Use when the game has GameAssembly plus il2cpp_data or global-metadata.dat and you need a disciplined workflow using Cpp2IL, Il2CppInterop, runtime inspection, and patch-target discovery instead of assuming managed game DLLs exist.
compatibility: opencode
metadata:
  audience: modders
  workflow: unity-il2cpp
---

## Goal

Turn an IL2CPP target into a usable list of symbols, interop types, runtime hooks, and candidate patch points.

## Recognize the target

Treat the game as IL2CPP when you see combinations like:
- `GameAssembly.dll` on Windows
- `global-metadata.dat`
- `*_Data/il2cpp_data/`
- no usable `Assembly-CSharp.dll` gameplay assembly in the normal Managed path

## Workflow

1. Confirm the IL2CPP markers.
2. Collect the binary and metadata files.
3. Use Cpp2IL output as the static-analysis starting point.
4. Use Il2CppInterop-oriented assemblies or generated stubs to reason about types and methods.
5. Use runtime inspection to validate static guesses, especially when names are stripped or weak.

## What to extract

Build an evidence list for:
- candidate gameplay classes
- UI controller classes
- singleton access points
- enums and data containers
- string anchors that match the user’s feature request
- native or generated method names that are stable enough to patch

## Decision rules

- Do not assume `Assembly-CSharp.dll` exists just because this repo template expects it.
- Do not present generated proxies as if they were original source.
- If symbol quality is poor, explicitly shift weight to runtime hierarchy inspection and asset naming.
- Prefer hooks near user-facing logic rather than deep generated wrappers.

## Output format

Return these sections:

### 1. IL2CPP evidence
List exact files proving the runtime.

### 2. Static-analysis path
State what dump or generated output is available and what it gives you.

### 3. Candidate targets
Give 3 to 7 targets with:
- type or generated proxy name
- likely feature relationship
- confidence level
- recommended validation step

### 4. Implementation advice
Explain whether to:
- continue static analysis
- pivot to runtime inspection
- adapt the repo for IL2CPP-specific plugin work

## Tooling assumptions

Use these tools conceptually when present:
- **Cpp2IL** to reverse Unity IL2CPP output toward managed-like assemblies
- **Il2CppInterop** to bridge CoreCLR and Il2Cpp at runtime and to work from generated assemblies
- **UnityExplorer or equivalent** to validate objects, components, and live method context

## Guardrails

- Keep the focus on local modding, debugging, and interoperability.
- Do not help with anti-cheat bypass, multiplayer cheating, or stealth injection techniques.
