---
name: store-metadata
description: "Trigger: /ios-metadata, /android-metadata, $store-metadata, App Store Connect, Play Console, App Privacy, Data Safety, ASO. Analyze the app repo and generate a complete store-metadata/index.html artifact for manual or EAS submission."
license: MIT
metadata:
  author: "Lyon Incode"
  version: "1.0.1"
---

# Store Metadata Generator

Analyze the developer's REAL app repository and generate ONE self-contained artifact — `store-metadata/index.html` — with every piece of metadata needed to publish to Apple App Store Connect (iOS) or Google Play Console (Android). Complete for fully manual submission; EAS mappings are added only when the repo uses EAS/Expo. Works with Expo, React Native, and native Swift/Kotlin apps.

## Activation Contract

| Input | Action |
| --- | --- |
| `/ios-metadata` or `$store-metadata ios` | iOS checklist only |
| `/android-metadata` or `$store-metadata android` | Android checklist only |
| No platform given | Detect from the repo; both platforms ship → one artifact, both groups |
| `$ARGUMENTS`, parsed in order | platform override (`ios`/`android`/`both`) → output path (contains `/` or ends `.html`) → target locale (`en-US`-style or bare two-letter code) → remaining words are app-name hints |

## Hard Rules

- Evidence first: every INFERRED value cites `file:line`. For generated/prebuild output, cite the full path and note it is generated (untracked) — never present it as a tracked source.
- Read-only on the analyzed repo: the only file written is the artifact.
- Never invent URLs, emails, account IDs, demo credentials, or legal claims; use clearly-marked placeholders (e.g. `https://example.com/privacy-policy`) flagged HUMAN REQUIRED. Privacy and data-safety answers MUST match the SDK/permission evidence actually found.
- If analysis is ambiguous, mark HUMAN REQUIRED rather than guess.

## Execution Steps

1. **Detect platform and framework.** Expo, bare React Native, native iOS, or native Android — detection signals are in [references/evidence-sources.md](references/evidence-sources.md). Detection drives every later step.
2. **Gather evidence** per platform following [references/evidence-sources.md](references/evidence-sources.md): app config, native build files and manifests, source + dependency scan for feature/SDK usage. Cite findings as you collect them.
3. **Fill every checklist field** from [references/ios-metadata-checklist.md](references/ios-metadata-checklist.md) and/or [references/android-metadata-checklist.md](references/android-metadata-checklist.md). Status each value: `INFERRED` (found in repo, cited), `SUGGESTED` (AI-drafted, with rationale), or `HUMAN REQUIRED` (state the exact decision the developer must make).
4. **Render the artifact.** Copy [assets/artifact-template.html](assets/artifact-template.html); replace every `{{TOKEN}}` and `<!-- FILL:section-id -->` block per its instructions comment; delete non-applicable conditional sections and record each deletion with its reason in the artifact footer.
5. **EAS mapping (optional).** When `eas.json`/Expo is detected, fill the artifact's EAS section from the "EAS / Expo Mapping Summary" of each checklist. When absent, state that the artifact is fully manual — every field row already carries its console screen path.

## Output Contract

- **File:** exactly one — `store-metadata/index.html` at the analyzed repo root (or the path from arguments). Self-contained: no external CSS, JS, or fonts. No `{{TOKEN}}` or FILL markers may survive. If the artifact exceeds the write-tool payload cap (100 KB+ is common), assemble via sequential chunked writes and verify completeness.
- **Language:** English by default; use the repo's primary language when clearly non-English; an explicit locale in arguments or user request overrides both. In every language: badge labels stay the canonical English enum; console navigation paths and config keys stay verbatim; Apple/Google reviewer-facing notes stay English.
- **Run summary (to the developer):** counts of INFERRED / SUGGESTED / HUMAN REQUIRED and the top 5 blocking decisions (these also fill the artifact's Blocking decisions section).

## References

- `references/evidence-sources.md` — per-platform repo-evidence inventory (step 2)
- `references/ios-metadata-checklist.md` — App Store Connect field checklist (step 3)
- `references/android-metadata-checklist.md` — Play Console field checklist (step 3)
- `assets/artifact-template.html` — artifact template with fill protocol (step 4)
- EAS build/submit flows: the `eas-app-stores` skill in this pack — EAS-flow oriented vs this skill's console-form checklists; complementary, not duplicated.

## Attribution

Original work of this skill pack, MIT licensed. Not derived from Expo. URLs, emails, passwords, and account IDs in examples are placeholders, never real values.
