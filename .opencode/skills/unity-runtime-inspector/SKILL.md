---
name: unity-runtime-inspector
description: Inspect a running Unity game to discover objects, components, UI, scenes, and live state with tools like UnityExplorer. Use when static code review is not enough, when symbols are weak or obfuscated, or when you need to map a visible feature to the exact GameObject and component chain that drives it.
compatibility: opencode
metadata:
  audience: modders
  workflow: runtime-debugging
---

## Goal

Use live inspection to bridge the gap between what the player sees and what the code actually drives.

## Best use cases

Use this skill when the user says things like:
- "Find the object behind this HUD element"
- "Which component owns this button?"
- "I found candidate methods but I need the runtime object path"
- "The names are obfuscated; how do I prove the right target?"

## Workflow

1. Reproduce the feature in-game.
2. Identify the visible object, scene, or state transition.
3. Traverse from the GameObject hierarchy to components and backing data.
4. Record exact object paths and component types.
5. Map those findings back to static code or interop stubs.

## What to capture

Always gather:
- current scene
- full GameObject path
- component type names on the object and parents
- serialized field names or current values that matter
- related singletons or managers in `DontDestroyOnLoad`
- event bindings, button listeners, or update methods when visible

## Practical heuristics

- For UI, start at `Canvas` -> panel -> control -> controller component.
- For gameplay systems, start with player object, target enemy object, or world manager singletons.
- For save or config behavior, trigger the change and watch which values move.
- When multiple similar objects exist, compare before/after state while toggling exactly one feature.

## Deliverable

Return:
- **Observed path:** exact runtime object path
- **Key components:** ordered list
- **Likely backing type or method:** with confidence
- **Static follow-up:** what assembly or proxy to inspect next
- **Patch candidate:** if one is now obvious

## Good behavior

- Separate direct observation from inference.
- Prefer a short reproducible investigation path over vague tool talk.
- If runtime inspection disproves an earlier static guess, say so clearly and replace it.
