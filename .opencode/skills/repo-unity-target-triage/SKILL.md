---
name: repo-unity-target-triage
description: Triage a target Unity game inside the bepinex-opencode template. Use when adapting the repo to a new game, checking whether the game is Mono or IL2CPP, locating Managed or il2cpp_data folders, verifying whether the existing build.sh and Plugin.csproj assumptions fit, and producing a concrete reverse-engineering plan before writing patches.
compatibility: opencode
metadata:
  audience: modders
  workflow: unity-bepinex
---

## Goal

Turn an unknown Unity game target into a concrete modding plan with clear next steps.

## Do this first

1. Inspect the repository assumptions before touching game code.
2. Fingerprint the target game runtime.
3. Decide whether the current repo fits as-is, needs light edits, or needs an IL2CPP-specific path.
4. End with a short action plan.

## Repository assumptions to verify

Treat this repo as a Mono-first template unless proven otherwise.

Check for these facts in the working tree:
- `build.sh` finds `UnityEngine.dll`, derives `MANAGED_DIR`, restores, then builds `src/Plugin.csproj` against that managed directory.
- `src/Plugin.csproj` references `UnityEngine.dll`, `UnityEngine.CoreModule.dll`, and `Assembly-CSharp.dll` from `$(ManagedDir)`.
- `src/Plugin.cs` uses a BepInEx plugin with `Harmony.PatchAll()`.

If the target game is IL2CPP, call that out explicitly. Do not pretend the stock `Assembly-CSharp.dll` workflow still applies.

## Runtime fingerprint checklist

Report these fields in a compact block:
- **Runtime:** `mono`, `il2cpp`, or `unclear`
- **Evidence:** exact files or folders observed
- **Unity version:** from logs, executable metadata, or asset headers if available
- **Game root:** path used for analysis
- **Managed dir:** path or `missing`
- **IL2CPP markers:** `GameAssembly.dll`, `global-metadata.dat`, `*_Data/il2cpp_data`, platform equivalents
- **BepInEx fit:** `green`, `yellow`, or `red`
- **Best next skill:** one of the other installed skills

## Decision rules

### Green
Use `green` when the game clearly exposes a Managed folder with `Assembly-CSharp.dll` and the repo can likely build against it with minor edits.

### Yellow
Use `yellow` when the game is moddable but the template needs adaptation, for example:
- extra game DLL references are required
- assembly names differ
- there are multiple candidate managed folders
- anti-tamper, obfuscation, or launcher indirection will slow iteration

### Red
Use `red` when the current template path is not the right starting point, especially when the game is IL2CPP and the work must begin with IL2CPP tooling, generated interop assemblies, or runtime inspection.

## Output format

Return exactly these sections:

### 1. Fingerprint
A concise bullet list using the checklist above.

### 2. Repo fit
State why the existing repo is or is not a fit.

### 3. Immediate edits
List the smallest required edits, such as:
- set `STEAM_APP_ID`
- rename plugin GUID/name/version
- add missing assembly references
- swap Mono workflow for IL2CPP workflow

### 4. Next investigation path
Pick one next skill and explain why.

## Guardrails

- Prefer evidence from files and logs over guesswork.
- If runtime is ambiguous, say so and list the two or three files needed to settle it.
- Do not recommend bypassing anti-cheat, DRM, account checks, or multiplayer protections.
