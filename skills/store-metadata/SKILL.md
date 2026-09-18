---
name: store-metadata
description: "Trigger: /ios-metadata, /android-metadata, $store-metadata, App Store Connect, Play Console, App Privacy, Data Safety, ASO. Analyze the app repo and generate a complete store-metadata/index.html artifact for manual or EAS submission."
license: MIT
metadata:
  author: "Lyon Incode"
  version: "1.0.1"
---

# Store Metadata Generator

One purpose: analyze the developer's REAL app repository and generate ONE self-contained artifact — `store-metadata/index.html` — with every piece of metadata needed to publish to Apple App Store Connect (iOS) or Google Play Console (Android). The artifact is complete for fully manual store submission; EAS mappings are added only when the repo actually uses EAS/Expo. Works with Expo, React Native, and native Swift/Kotlin apps.

Original work of this pack (MIT), not derived from Expo's `eas-app-stores`. Read-only on the analyzed repo: the ONLY file written is the artifact.

## When to use

- `/ios-metadata` (or `$store-metadata ios`) → iOS checklist only.
- `/android-metadata` (or `$store-metadata android`) → Android checklist only.
- No platform given → detect from the repo; when both platforms ship, generate both groups in one artifact.
- Parse `$ARGUMENTS` tokens in order: platform override (`ios`, `android`, `both`), an output path (contains `/` or ends `.html`), a target locale (`en-US`-style or a bare two-letter code), then remaining words as app-name hints.

## Workflow

1. **Detect platform and framework.** Expo (`app.json`, `app.config.ts/js`, `expo` in `package.json`); bare React Native (`react-native` dep + `android/`, `ios/`); native iOS (`*.xcodeproj`, `Info.plist`); native Android (`AndroidManifest.xml`, `build.gradle(.kts)`). Record which apply — detection drives every later step.
2. **Gather evidence.** Inspect the analyzed repo per platform following [references/evidence-sources.md](references/evidence-sources.md): app config, native iOS/Android build files and manifests, and a source + dependency scan for feature/SDK usage. Cite every finding as you collect it.
3. **Fill every checklist field** from [references/ios-metadata-checklist.md](references/ios-metadata-checklist.md) and/or [references/android-metadata-checklist.md](references/android-metadata-checklist.md). Status each value:
   - `INFERRED` — found in the repo; cite evidence as `file:line`.
   - `SUGGESTED` — AI-drafted from the analysis; give the rationale.
   - `HUMAN REQUIRED` — cannot be derived; state exactly what decision the developer must make.

   Never invent URLs, account IDs, demo credentials, or legal claims; use clearly-marked placeholders such as `https://example.com/privacy-policy` flagged HUMAN REQUIRED. Privacy and data-safety answers MUST be consistent with the SDK/permission evidence actually found.
4. **Render the artifact.** Copy [assets/artifact-template.html](assets/artifact-template.html), replace every `{{TOKEN}}` and every `<!-- FILL:section-id -->` block per the instructions comment inside it, delete unused platform sections, and write to `store-metadata/index.html` at the analyzed repo root (or the path from arguments). Keep the output self-contained: no external CSS, JS, or fonts. Both-platform artifacts regularly exceed 100 KB; when the write tool enforces a payload limit, assemble the file with sequential chunked writes (create + append) and verify completeness (no leftover tokens or FILL markers) at the end.
   - Artifact language: English by default. If the analyzed repo's primary language (UI copy, store-facing text) is clearly non-English, generate the artifact in that language. An explicit locale in arguments or an explicit user request overrides both. In every language: status badge labels stay the canonical English enum (INFERRED / SUGGESTED / HUMAN REQUIRED), console navigation paths and config keys stay verbatim, and Apple/Google reviewer-facing notes stay in English because store reviewers read English.
5. **EAS mapping (optional).** When `eas.json`/Expo is detected, fill the artifact's EAS section from the "EAS / Expo Mapping Summary" of each checklist (submit config keys, `expo.ios.infoPlist` additions, service-account key names, `track`, `releaseStatus`, metadata delivery). When absent, state that the artifact is fully manual — every field row already carries its console screen path.

## Hard rules

- Evidence first: every INFERRED value cites `file:line`. For generated/prebuild output (merged manifests, built `Info.plist`, config-plugin output), cite the full path and note that it is generated (untracked) output — never present it as a tracked source.
- Do not modify the analyzed repo's source or config; the only file written is the artifact (plus nothing else).
- If analysis is ambiguous, mark HUMAN REQUIRED rather than guess.
- Placeholders only for credentials, emails, account IDs, and URLs not found in the repo.
- End the run with a summary: counts of INFERRED / SUGGESTED / HUMAN REQUIRED and the top 5 blocking decisions (these also fill the artifact's Blocking decisions section).

## Related

- EAS build/submit flows themselves: the `eas-app-stores` skill (`skills/eas-app-stores/SKILL.md` in this pack). Its references are EAS-flow oriented; this skill's checklists are console-form oriented. Complementary and cross-linked, not duplicated.

## Attribution

Original work of this skill pack, MIT licensed. Not derived from Expo. URLs, emails, passwords, and account IDs in examples are placeholders, never real values.
