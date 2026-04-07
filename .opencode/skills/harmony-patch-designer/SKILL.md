---
name: harmony-patch-designer
description: Design robust Harmony or HarmonyX patches for Unity and BepInEx mods. Use when you already know candidate classes or methods and need to choose prefix, postfix, transpiler, or finalizer strategies, define binding details, reduce version fragility, and produce a clear implementation and verification plan.
compatibility: opencode
metadata:
  audience: modders
  workflow: patch-planning
---

## Goal

Convert a recon result into a patch design that is specific, testable, and maintainable.

## Required input

You should have at least one candidate target method or a very small set of candidates.

If the target list is still broad, send the user to `mono-assembly-recon`, `il2cpp-recon`, or `unity-runtime-inspector` first.

## Patch strategy rules

### Prefer prefix when
- you need to block the original method
- you need to validate or replace inputs early
- the feature is a simple allow/deny gate

### Prefer postfix when
- you need to observe results
- you need to tweak return values or side effects after normal execution
- you want the safest first patch on an unfamiliar method

### Prefer transpiler only when
- there is no clean boundary method
- a narrow branch or constant must be changed in-place
- you can describe the IL edit in stable semantic terms

### Add a finalizer when
- the patch must log or absorb predictable exceptions without killing gameplay

## Design checklist

For each patch, specify:
- target type and exact method signature
- why this method is the right boundary
- chosen patch type and why it beats the alternatives
- whether the patch should skip the original method
- what config flag should guard it
- what log line proves it works without spamming
- failure mode if the method changes in a game update

## Stability rules

- Prefer semantic feature boundaries over utility helpers.
- Avoid broad hot-path patches if a narrower event or validator exists.
- Be explicit about overloads, generics, nested types, and async or coroutine wrappers.
- If using a transpiler, describe the anchor instructions by intent, not only by raw offsets.

## Output format

Return exactly:

### Patch plan
A bullet list with the chosen target and patch type.

### Why this is the safest hook
Two to four sentences.

### Skeleton requirements
List the method signature details, required references, and any state you need to capture.

### Verification steps
A minimal manual test plan.

### Fallback hook
One backup method if the primary target proves unstable.

## Guardrails

- Default to the least invasive hook that can solve the problem.
- Do not propose stealth, persistence, anti-cheat evasion, or remote abuse.
