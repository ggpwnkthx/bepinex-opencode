---
name: unity-asset-recon
description: Extract and inspect Unity assets, prefabs, scenes, and bundles for modding workflows. Use when art, UI layout, prefab structure, scene composition, or asset naming matters; when code names are unclear; or when you need to map assets back to runtime objects using tools like AssetRipper.
compatibility: opencode
metadata:
  audience: modders
  workflow: asset-analysis
---

## Goal

Use asset-level evidence to complement code-level reverse engineering and to decide whether a mod is primarily an asset edit, a code patch, or both.

## Best use cases

- UI layout changes
- prefab component discovery
- scene composition questions
- locating icons, audio, models, materials, or animation names
- finding stable strings or asset names that can lead back to code

## Workflow

1. Open the game folder or bundles with an asset extraction tool.
2. Search for scene names, prefab names, UI labels, sprite names, audio cues, and addressable keys related to the feature.
3. Build an asset map.
4. Connect asset names back to runtime object paths or code symbols.

## What to record

- scene or bundle name
- prefab or object name
- component types present on important prefabs
- referenced sprites, fonts, materials, clips, or animation controllers
- addressable or resource key names when visible
- any strings that also appear in assemblies or logs

## Decision rules

- If the feature is entirely data-driven, say that an asset route may be simpler than a Harmony patch.
- If the asset reveals a controller or prefab name, feed that name into static or runtime recon.
- If the asset structure and code path disagree, trust the live game and explain the mismatch.

## Deliverable

Return:
- **Asset map:** the few assets that matter most
- **Likely runtime connection:** object path, controller, or assembly string to chase next
- **Recommended mod route:** asset-only, code-only, or hybrid
- **Open questions:** anything still missing

## Good behavior

- Use asset evidence to reduce guesswork, not to pad the answer.
- Prefer names that can be reused as search anchors elsewhere in the repo or game files.
