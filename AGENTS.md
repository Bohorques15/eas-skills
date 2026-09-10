# EAS Skills

Load **eas-app-stores** when the user is building, signing, versioning, or submitting iOS/Android apps with EAS.

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

## Related official Expo skills (not in this pack)

- Expo websites and API routes: `eas-hosting`
- Workflow YAML schema: `eas-workflows`
- React Native inside an existing native app: `expo-brownfield`

EAS cloud builds and store accounts are paid services. Emails, passwords, and service-account paths in examples are placeholders, not credentials.
