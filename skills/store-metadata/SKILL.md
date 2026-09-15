---
name: store-metadata
description: Analyze the current app repository and generate one complete, self-contained store metadata artifact (store-metadata/index.html) covering every field App Store Connect (iOS) or Google Play Console (Android) requires to publish. Invoke via /ios-metadata, /android-metadata, or $store-metadata ios|android. Works with Expo, React Native, and native Swift/Kotlin apps. EAS is optional — the artifact is complete for fully manual submission and adds EAS mappings when the repo uses EAS. Use for store listing metadata, console field checklists, App Privacy and Data Safety answers, permission rationale copy, screenshot and icon specs, keywords and ASO copy.
version: 1.0.0
license: MIT
---

# Store Metadata Generator

One purpose: analyze the developer's REAL app repository and generate ONE self-contained artifact — `store-metadata/index.html` — with every piece of metadata needed to publish to Apple App Store Connect (iOS) or Google Play Console (Android). The artifact is complete for fully manual store submission; EAS mappings are added only when the repo actually uses EAS/Expo.

Original work of this pack (MIT), not derived from Expo's `eas-app-stores`. Read-only on the analyzed repo: the ONLY file written is the artifact.

## When to use

- `/ios-metadata` (or `$store-metadata ios`) → iOS checklist only.
- `/android-metadata` (or `$store-metadata android`) → Android checklist only.
- No platform given → detect from the repo; when both platforms ship, generate both groups in one artifact.
- Parse `$ARGUMENTS` tokens in order: platform override (`ios`, `android`, `both`), an output path (contains `/` or ends `.html`), a target locale (`en-US`-style or a bare two-letter code), then remaining words as app-name hints.

## Workflow

1. **Detect platform and framework.** Expo (`app.json`, `app.config.ts/js`, `expo` in `package.json`); bare React Native (`react-native` dep + `android/`, `ios/`); native iOS (`*.xcodeproj`, `Info.plist`); native Android (`AndroidManifest.xml`, `build.gradle(.kts)`). Record which apply — detection drives every later step.
2. **Gather evidence.** Inspect, per platform:
   - App config: name, slug, `bundleIdentifier` / `package` (applicationId), version, `buildNumber`/`versionCode`, icon, splash, plugins, `expo.ios.infoPlist`, `expo.android.permissions`.
   - Native iOS: `NS*UsageDescription` keys in `Info.plist`, `CFBundle*` and `PRODUCT_BUNDLE_IDENTIFIER` in `project.pbxproj`, entitlements, `Podfile`.
   - Native Android: `AndroidManifest.xml` permissions/intents, `strings.xml`, signing and version config in gradle files.
   - Source code + dependency manifests (`package.json`, `Podfile`, `build.gradle`) for feature/SDK usage: camera, microphone, location, contacts, photos, health (HealthKit / Health Connect), payments and IAP, push notifications, analytics, ad SDKs (AdMob, AppLovin, Facebook Ads), social login, WebViews, deep links, encryption libs.
3. **Fill every checklist field** from [references/ios-metadata-checklist.md](references/ios-metadata-checklist.md) and/or [references/android-metadata-checklist.md](references/android-metadata-checklist.md). Status each value:
   - `INFERRED` — found in the repo; cite evidence as `file:line`.
   - `SUGGESTED` — AI-drafted from the analysis; give the rationale.
   - `HUMAN REQUIRED` — cannot be derived; state exactly what decision the developer must make.

   Never invent URLs, account IDs, demo credentials, or legal claims; use clearly-marked placeholders such as `https://example.com/privacy-policy` flagged HUMAN REQUIRED. Privacy and data-safety answers MUST be consistent with the SDK/permission evidence actually found.
4. **Render the artifact.** Copy [assets/artifact-template.html](assets/artifact-template.html), replace every `{{TOKEN}}` and every `<!-- FILL:section-id -->` block per the instructions comment inside it, delete unused platform sections, and write to `store-metadata/index.html` at the analyzed repo root (or the path from arguments). Keep the output self-contained: no external CSS, JS, or fonts.
   - Artifact language: English by default. If the analyzed repo's primary language (UI copy, store-facing text) is clearly non-English, generate the artifact in that language. An explicit locale in arguments or an explicit user request overrides both. In every language: status badge labels stay the canonical English enum (INFERRED / SUGGESTED / HUMAN REQUIRED), console navigation paths and config keys stay verbatim, and Apple/Google reviewer-facing notes stay in English because store reviewers read English.
5. **EAS mapping (optional).** When `eas.json`/Expo is detected, fill the artifact's EAS section: `eas.json` submit config keys, `expo.ios.infoPlist` suggested additions, the Play service-account key (`serviceAccountKeyPath`, historically `googleServicesAccount`), `track`, `releaseStatus`, and metadata delivery via `eas submit` / `eas metadata` / ASC API / Play API. When absent, the section states the artifact is fully manual — every field row already carries its console screen path.

## Hard rules

- Evidence first: every INFERRED value cites `file:line`.
- Do not modify the analyzed repo's source or config; the only file written is the artifact (plus nothing else).
- If analysis is ambiguous, mark HUMAN REQUIRED rather than guess.
- Placeholders only for credentials, emails, account IDs, and URLs not found in the repo.
- End the run with a summary: counts of INFERRED / SUGGESTED / HUMAN REQUIRED and the top 5 blocking decisions (these also fill the artifact's Blocking decisions section).

## Related

- EAS build/submit flows themselves: the `eas-app-stores` skill (`skills/eas-app-stores/SKILL.md` in this pack). Its references are EAS-flow oriented; this skill's checklists are console-form oriented. Complementary and cross-linked, not duplicated.

## Attribution

Original work of this skill pack, MIT licensed. Not derived from Expo. URLs, emails, passwords, and account IDs in examples are placeholders, never real values.
