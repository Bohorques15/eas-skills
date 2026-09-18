# Repo Evidence Sources (workflow step 2)

Inventory of what to inspect in the analyzed repository before filling the checklists. Cite every finding as you collect it (`file:line` for tracked sources; full path + generated-output note for prebuild files — see SKILL.md Hard Rules).

## App config (all frameworks)

- App config: name, slug, `bundleIdentifier` / `package` (applicationId), version, `buildNumber`/`versionCode`, icon, splash, plugins, `expo.ios.infoPlist`, `expo.android.permissions`.

## Native iOS

- Native iOS: `NS*UsageDescription` keys in `Info.plist`, `CFBundle*` and `PRODUCT_BUNDLE_IDENTIFIER` in `project.pbxproj`, entitlements, `Podfile`.

## Native Android

- Native Android: `AndroidManifest.xml` permissions/intents, `strings.xml`, signing and version config in gradle files.

## Source code + dependencies (feature/SDK usage)

- Source code + dependency manifests (`package.json`, `Podfile`, `build.gradle`) for feature/SDK usage: camera, microphone, location, contacts, photos, health (HealthKit / Health Connect), payments and IAP, push notifications, analytics, ad SDKs (AdMob, AppLovin, Facebook Ads), social login, WebViews, deep links, encryption libs.

## Framework-specific authority

- **Expo/bare-RN iOS:** the prebuilt `Info.plist` may carry plugin-injected English boilerplate usage strings — treat as blocking-equivalent (iOS checklist §10).
- **Expo/bare-RN Android:** the authoritative permission set is the merged manifest under `android/app/build/intermediates/merged_manifests/` — the checked-in template manifest and `expo.android.permissions` are inputs only (Android checklist §12).
- **Never open credential contents** (`google-services.json`, service-account keys, keystores, `.env` values): cite file names as evidence, never values.
