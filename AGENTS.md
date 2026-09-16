# EAS Skills

Load **eas-app-stores** when the user is building, signing, versioning, or submitting iOS/Android apps with EAS. Load **store-metadata** when the user wants store listing metadata generated from a repository (see below).

## Triggers

Use this skill when the request involves any of:

- EAS Build / EAS Submit / `eas.json` / `eas credentials`
- TestFlight, App Store, Google Play, App Store Connect
- `eas build`, `eas submit`, `--auto-submit`, store listing metadata / ASO
- Native Swift/SwiftUI/UIKit distribution via EAS (no Expo runtime)

## How to load

1. Read `skills/eas-app-stores/SKILL.md`.
2. Then open only the matching reference:

| Topic | File |
| --- | --- |
| CI/CD and EAS Workflows | `skills/eas-app-stores/references/workflows.md` |
| TestFlight | `skills/eas-app-stores/references/testflight.md` |
| iOS App Store review | `skills/eas-app-stores/references/ios-app-store.md` |
| App Store metadata / ASO | `skills/eas-app-stores/references/app-store-metadata.md` |
| Google Play | `skills/eas-app-stores/references/play-store.md` |
| Native Swift iOS (no RN runtime) | `skills/eas-app-stores/references/native-ios.md` |

## store-metadata skill

Load **store-metadata** when the request involves any of:

- Generating store listing metadata from the developer's app repository
- App Store Connect or Google Play Console field checklists (App Privacy, Data Safety, permissions, screenshots, age/content ratings)
- A publishable metadata artifact (`store-metadata/index.html`)
- The `/ios-metadata` or `/android-metadata` commands (Codex: `$store-metadata ios|android`)

### How to load

1. Read `skills/store-metadata/SKILL.md`.
2. Then open only the matching file:

| Topic | File |
| --- | --- |
| App Store Connect field checklist | `skills/store-metadata/references/ios-metadata-checklist.md` |
| Google Play Console field checklist | `skills/store-metadata/references/android-metadata-checklist.md` |
| Output artifact template | `skills/store-metadata/assets/artifact-template.html` |

EAS is optional for this skill: the artifact is complete for fully manual store submission and adds EAS mappings only when the repo uses EAS/Expo. The skill is read-only on the analyzed repo — the only file written is the artifact. Its checklists are console-form oriented and complement (never duplicate) the EAS-flow references of `eas-app-stores`.

## Related official Expo skills (not in this pack)

- Expo websites and API routes: `eas-hosting`
- Workflow YAML schema: `eas-workflows`
- React Native inside an existing native app: `expo-brownfield`

EAS cloud builds and store accounts are paid services. Emails, passwords, and service-account paths in examples are placeholders, not credentials.
