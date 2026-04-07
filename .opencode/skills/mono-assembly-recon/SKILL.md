---
name: mono-assembly-recon
description: Reverse engineer Unity Mono games for BepInEx mods. Use when the target game exposes a Managed folder and you need to inspect Assembly-CSharp or other managed DLLs, map gameplay or UI logic, identify candidate Harmony patch targets, and convert static analysis into a modding plan.
compatibility: opencode
metadata:
  audience: modders
  workflow: unity-mono
---

## Goal

Map a Mono Unity feature request to concrete types, methods, fields, and Harmony patch candidates.

## Inputs you should expect

Typical prompts include:
- "Find where inventory capacity is enforced"
- "Locate the method behind this settings toggle"
- "Identify a safe patch point for damage calculation"
- "Explain how this UI button is wired"

## Workflow

1. Open the main gameplay assemblies first:
   - `Assembly-CSharp.dll`
   - other non-Unity game DLLs in `Managed/`
   - optionally plugin or framework DLLs that look game-specific
2. Search from the user-facing symptom inward:
   - exact UI labels
   - localization keys
   - config keys
   - item names, buff names, scene names, prefab names
   - exception strings and log messages
3. Build a type map:
   - manager singletons
   - MonoBehaviours controlling the target feature
   - data models and save structs
   - methods called from button handlers, update loops, or RPC boundaries
4. Narrow to stable patch points.

## Heuristics that work well

- Prefer methods with semantic names over low-level helpers.
- Prefer feature boundaries over hot loops.
- Search for `Awake`, `Start`, `OnEnable`, `Update`, `FixedUpdate`, coroutine state machines, and event handlers.
- When UI is involved, search for `Button`, `Toggle`, `TMP`, `TextMeshPro`, `Canvas`, `OnClick`, and scene-specific controller names.
- When data is involved, search constructors, serializers, save/load entry points, and validation methods.

## Patch-target selection rules

For every candidate, capture:
- fully qualified type name
- method name and signature
- why it is relevant
- patch shape: prefix, postfix, transpiler, or no patch yet
- stability notes: overloaded, generic, frequently called, or likely version-fragile

Prefer:
- **Prefix** to short-circuit validation or gate behavior
- **Postfix** to observe results or tweak outputs safely
- **Transpiler** only when there is no cleaner hook

## Deliverable

Return a table with these columns:
- `Target`
- `Why it matters`
- `Suggested patch`
- `Risk`
- `What to verify in runtime`

Then add:
- **Best primary target**
- **Fallback target**
- **Unknowns still blocking implementation**

## Good behavior

- Explain your reasoning from symbols, strings, and call relationships.
- Distinguish high-confidence matches from weak guesses.
- If code is obfuscated, pivot to string usage, field shapes, call sites, and runtime inspection rather than inventing certainty.
