# App Store Connect Metadata Checklist (iOS)

Console-form checklist for publishing an iOS app to Apple App Store Connect (ASC). One row per field: limits, exact console location, how to derive the value from the repository, status guidance, and the EAS/Expo mapping. Fill the results into `../assets/artifact-template.html` following `../SKILL.md`.

**Status rules.** `INFERRED` requires a `file:line` citation from the analyzed repo. `SUGGESTED` requires a rationale. `HUMAN REQUIRED` states the exact decision the developer must make. Never invent URLs, account IDs, demo credentials, or legal claims — use a clearly-marked placeholder (e.g. `https://example.com/privacy-policy`) flagged HUMAN REQUIRED.

**Console path convention.** `My Apps → [App] → App Information`, `My Apps → [App] → App Store → [Version]`, `My Apps → [App] → App Privacy`. Apple renames nav items occasionally; when a label differs, search the field name in the ASC help search.

**EAS-flow depth (not duplicated here).** If the `eas-app-stores` skill is installed alongside this one, see [app-store-metadata.md](../../eas-app-stores/references/app-store-metadata.md) for `store.config.json` (EAS Metadata) and ASO strategy, and [ios-app-store.md](../../eas-app-stores/references/ios-app-store.md) for submission mechanics and the App Review process.

## 1. App Information

Console: `My Apps → [App] → App Information`. App-level, shared across versions. Name and Subtitle are localized per storefront.

| Field | Limits / format | Where in ASC | How to derive from repo | Status guidance | EAS / Expo mapping |
| --- | --- | --- | --- | --- | --- |
| App Name | 30 chars, per locale | App Information → Name | `expo.name` in `app.json`/`app.config.ts`; native: `CFBundleDisplayName` in `Info.plist`, `PRODUCT_NAME` in `project.pbxproj` | INFERRED when found. SUGGESTED brand+keyword variant for ASO (brand first, one strong keyword, no filler words like "app") | `app.json` → `expo.name`; `store.config.json` → `apple.info.<locale>.title` |
| Subtitle | 30 chars, per locale | App Information → Subtitle | Not stored in repo; draft from the app's value proposition (README, onboarding screens, feature list) | SUGGESTED with rationale; do not repeat words already in the Name (Apple indexes each word once); HUMAN REQUIRED for final brand voice | `store.config.json` → `apple.info.<locale>.subtitle` |
| Privacy Policy URL | Public, working https URL; required | App Information → Privacy Policy URL | Search repo/README/settings screens for an existing published policy URL | INFERRED only when a real URL exists in the repo; otherwise placeholder `https://example.com/privacy-policy` + HUMAN REQUIRED (must be authored and hosted) | `store.config.json` → `apple.info.<locale>.privacyPolicyUrl` |
| Primary Category | Apple category enum (e.g. `HEALTH_AND_FITNESS`, `FINANCE`, `UTILITIES`, `GAMES` with subcategory) | App Information → Primary Category | Infer from features/deps (health SDKs → Health & Fitness; billing/IAP-heavy → Finance; etc.) | SUGGESTED from evidence; HUMAN REQUIRED to confirm business positioning. `KIDS` category imposes extra rules — never suggest it without explicit human intent | `store.config.json` → `apple.categories[0]` |
| Secondary Category | Same enum, optional | App Information → Secondary Category | Infer second-strongest fit | SUGGESTED / optional | `store.config.json` → `apple.categories[1]` |
| Copyright | e.g. `2026 Your Company, Inc.` — year + legal rights holder, no "©" symbol needed | App Information → Copyright | `LICENSE` file, `package.json` author, org name in `project.pbxproj` | SUGGESTED; HUMAN REQUIRED to confirm the legal entity name | `store.config.json` → `apple.copyright` |
| Bundle ID | Reverse-DNS, fixed once the app record exists | Chosen at app-record creation; must match the uploaded binary | `expo.ios.bundleIdentifier`; native: `PRODUCT_BUNDLE_IDENTIFIER` in `project.pbxproj` | INFERRED (cite file); flag mismatch between config and native project if both exist | `app.json` → `expo.ios.bundleIdentifier` |
| SKU | Unique internal ID, not user-visible | Chosen at app-record creation | Not derivable | HUMAN REQUIRED; suggest a slug-based value like `myapp-ios-001` | — |
| Primary language | Locale of the default listing | App Information → Primary Language | Default locale of app strings (`CFBundleDevelopmentRegion`, i18n resources) | SUGGESTED | — |

## 2. Version — Listing and Build

Console: `My Apps → [App] → App Store → [Version]`. Per-version, localized fields plus the build selector.

| Field | Limits / format | Where in ASC | How to derive from repo | Status guidance | EAS / Expo mapping |
| --- | --- | --- | --- | --- | --- |
| Description | 4000 chars, per locale; first ~3 lines visible before "more" | Version page → Description | Draft from README, feature list, onboarding copy; front-load benefits, use bullets for features | SUGGESTED (every claim — user counts, awards, guarantees — is HUMAN REQUIRED; never fabricate social proof) | `store.config.json` → `apple.info.<locale>.description` |
| Keywords | 100 chars, comma-separated, no spaces after commas; hidden from users | Version page → Keywords | Derive candidate terms from features, deps, and domain vocabulary | SUGGESTED following ASO rules: no duplicates of Name/Subtitle words, singular forms, digits over words, no filler. Search-volume claims are HUMAN REQUIRED (needs ASO tooling) | `store.config.json` → `apple.info.<locale>.keywords` |
| Support URL | Public, working URL; required | Version page → Support URL | Search repo for support/help/contact URLs (README, in-app links) | INFERRED if a real URL exists; otherwise placeholder + HUMAN REQUIRED | `store.config.json` → `apple.info.<locale>.supportUrl` |
| Marketing URL | Optional public URL | Version page → Marketing URL | Search repo for the product website | INFERRED if found; otherwise optional placeholder — HUMAN REQUIRED (or leave empty) | `store.config.json` → `apple.info.<locale>.marketingUrl` |
| Promotional Text | 170 chars; updatable without a new build | Version page → Promotional Text | Draft from current campaigns/seasonal hooks | SUGGESTED / optional; campaign claims HUMAN REQUIRED | `store.config.json` → `apple.info.<locale>.promoText` |
| What's New (release notes) | 4000 chars, per locale | Version page → What's New | `CHANGELOG.md`, git log since the last release tag | INFERRED from changelog entries when present; SUGGESTED summary otherwise; first release → standard "initial release" copy | `store.config.json` → `apple.info.<locale>.releaseNotes` |
| Version | User-facing `CFBundleShortVersionString`, e.g. `1.2.3` | Version page (version record) | `expo.version`; native: `MARKETING_VERSION` in `project.pbxproj`, `CFBundleShortVersionString` in `Info.plist` | INFERRED; flag mismatches between config and native values | `app.json` → `expo.version`; `eas build:version:set -p ios` |
| Build number | Integer `CFBundleVersion`; must increase on every upload | Version page → Build section (after upload) | `expo.ios.buildNumber`; native: `CURRENT_PROJECT_VERSION` in `project.pbxproj` | INFERRED; note when EAS remote versioning (`appVersionSource: remote` + `autoIncrement`) owns this value instead | `eas.json` → `cli.appVersionSource`, `build.<profile>.autoIncrement` |

## 3. App Review Information

Console: `My Apps → [App] → App Store → [Version] → App Review`. Apple reviewers must be able to exercise the app end-to-end.

| Field | Limits / format | Where in ASC | How to derive from repo | Status guidance | EAS / Expo mapping |
| --- | --- | --- | --- | --- | --- |
| Contact first / last name | Real person reachable during review | App Review → Contact | Not derivable (do not mine personal names from git history without consent) | HUMAN REQUIRED | `store.config.json` → `apple.review.firstName` / `lastName` |
| Contact phone | Reachable number with country code | App Review → Contact | Not derivable | HUMAN REQUIRED — placeholder `+1 555-000-0000` | `store.config.json` → `apple.review.phone` |
| Contact email | Monitored inbox | App Review → Contact | Generic team address in README/`package.json` author field may be a hint | HUMAN REQUIRED — placeholder `app-review-contact@example.com` | `store.config.json` → `apple.review.email` |
| Sign-in required | Checkbox: does review need an account? | App Review → Sign-in required | Detect auth flows: login screens, auth deps (`firebase` auth, `expo-auth-session`, `@react-native-async-storage` + session tokens, Keychain usage) | INFERRED (cite the auth evidence) when clearly present/absent; otherwise SUGGESTED with rationale | — |
| Demo account (username / password) | Working credentials for reviewers | App Review → Sign-in (when required) | NEVER derive or invent credentials | HUMAN REQUIRED — the developer must create a dedicated demo account with seeded data. Placeholder `demo@example.com` / `REPLACE_WITH_DEMO_PASSWORD` | `store.config.json` → `apple.review.demoUsername` / `demoPassword` |
| Review notes | Free text: how to test key flows | App Review → Notes | Draft step-by-step flows from README, e2e tests, deep-link schemes, hardware/pairing needs | SUGGESTED; anything hardware- or region-dependent is HUMAN REQUIRED | `store.config.json` → `apple.review.notes` |
| Attachment | Optional file (demo video, PDF guide) | App Review → Attachment | Not derivable | HUMAN REQUIRED guidance: attach a short walkthrough video when flows need hardware, pairing, QR codes, or invite codes | — |

## 4. Pricing and Availability

Console: `My Apps → [App] → App Store → [Version] → Pricing and Availability`.

| Field | Limits / format | Where in ASC | How to derive from repo | Status guidance | EAS / Expo mapping |
| --- | --- | --- | --- | --- | --- |
| Price | Free or a price tier | Pricing and Availability → Price | Context only: IAP/subscription deps (`react-native-iap`, StoreKit, `expo-in-app-purchases`) suggest a freemium model | HUMAN REQUIRED business decision; SUGGESTED default `Free` when IAP is present | ASC API price schedule; not covered by `store.config.json` |
| Availability (territories) | Storefront selection | Pricing and Availability → Availability | Localization coverage may hint at target markets | HUMAN REQUIRED | — |
| Pre-order | Optional future release date | Pricing and Availability → Pre-Order | Not derivable | HUMAN REQUIRED / optional | — |
| Distribution method | Public, unlisted app, or Custom App (business) | App creation / Distribution | Not derivable | HUMAN REQUIRED | — |

## 5. App Privacy

Console: `My Apps → [App] → App Privacy`. The privacy "nutrition label" is generated from this questionnaire and shown publicly on the Product Page. Answers MUST match (a) the SDK/permission evidence found in the repo and (b) the privacy policy text. Google Play's counterpart is the Data Safety form — see the Android checklist.

**Per data type, the questionnaire asks:** Collected? → Used for tracking? → Linked to the user's identity? → Purposes. Purpose enum: Third-Party Advertising, Developer's Advertising or Marketing, Analytics, App Functionality, Product Personalization, Other.

### Apple data-type taxonomy and repo detection

| Data category (Apple) | Included data types | Detect from repo (evidence to cite) |
| --- | --- | --- |
| Contact Info | Name, Email Address, Phone Number | Auth/account SDKs, profile forms, Firebase Auth, Auth0, backend sign-up endpoints |
| Health & Fitness | Health data, fitness data | HealthKit entitlement, `NSHealth*UsageDescription`, `react-native-health`, `expo-health` (sensitive: see §6) |
| Financial Info | Payment Info, Credit Info | IAP/StoreKit, `stripe`, `braintree`, card-entry forms (payment info entered via Apple IAP is still collected); financial/fintech features draw extra review scrutiny |
| Location | Precise Location, Coarse Location | `NSLocation*UsageDescription`, `expo-location`, `react-native-maps`, CoreLocation calls, IP-based geolocation |
| Sensitive Info | Racial/ethnic data, sexual orientation, pregnancy/childbirth, religion, philosophical/political beliefs, trade-union membership, biometric data, health records and more | Only with explicit features (don't assume); always HUMAN REQUIRED unless code plainly handles such fields |
| Contacts | Contacts (address book) | `NSContactsUsageDescription`, Contacts framework, `expo-contacts` |
| User Content | Photos or Videos, Audio or Voice Data, Gameplay Content, Customer Support, Other User Content | Camera/photos/mic usage keys, upload endpoints, in-app chat/support SDKs, UGC posting features |
| Browsing History | Web browsing history within the app | WebView-heavy apps that log visited URLs (not merely embedding a WebView) |
| Search History | In-app searches | Search screens/history persistence for user queries |
| Identifiers | User ID, Device ID | Analytics SDKs (Firebase Analytics, Amplitude, Segment, Mixpanel), account systems, IDFA usage |
| Usage Data | Product Interaction, Other Usage Data | Analytics event tracking, session recording, feature-flag telemetry |
| Diagnostics | Crash Data, Performance Data, Other Diagnostic Data | Crashlytics, Sentry, Bugsnag, performance monitoring SDKs |
| Purchases | Purchase history | StoreKit/IAP, `react-native-iap`, receipt validation endpoints, subscription managers (RevenueCat) |

### Tracking, IDFA, and ATT

| Item | Requirement | Detect from repo | Status guidance |
| --- | --- | --- | --- |
| Data "used for tracking" | Declare YES when data is shared with third parties for their advertising/brokerage (cross-app, cross-site) | Ad SDKs (AdMob, AppLovin, Facebook Audience Network), attribution SDKs (AppsFlyer, Adjust, Branch) | INFERRED from deps; borderline cases HUMAN REQUIRED |
| IDFA collection | Requires AppTrackingTransparency permission before access | `AppTrackingTransparency` framework, `react-native-tracking-transparency`, AdMob IDFA integration | INFERRED; if IDFA is collected → also answer tracking questions and add the usage string (see §10) |
| `NSUserTrackingUsageDescription` | Mandatory when ATT prompt is shown | `Info.plist`, `expo.ios.infoPlist` | INFERRED if present; if ATT code exists but the key is missing, flag as a blocking build issue |
| Privacy nutrition labels | Auto-generated summary shown on the Product Page; must stay consistent with the policy and SDK behavior | Derived from this section's answers | Every label answer SUGGESTED-from-evidence or HUMAN REQUIRED, never guessed |
| Privacy policy | Required, public URL; must describe collection, use, sharing, retention, and deletion | Existing policy URL in repo | INFERRED if real URL found; otherwise HUMAN REQUIRED |

## 6. Health Apps (HealthKit)

Applies only when HealthKit / health data evidence exists; otherwise the generating agent deletes the artifact section entirely per the template's conditional-section rule and records the omission and its reason in the artifact footer.

| Field | Limits / format | Where in ASC | How to derive from repo | Status guidance | EAS / Expo mapping |
| --- | --- | --- | --- | --- | --- |
| `NSHealthShareUsageDescription` | Required string to READ HealthKit data; must name the data and the benefit | `Info.plist` (binary), surfaces in system permission dialog | HealthKit entitlement + read calls in source | INFERRED if present; SUGGESTED copy otherwise (formula: "[App] reads [specific data] to [benefit]") — vague copy is rejected (Guideline 5.1.1) | `expo.ios.infoPlist.NSHealthShareUsageDescription` |
| `NSHealthUpdateUsageDescription` | Required string to WRITE HealthKit data | `Info.plist` | HealthKit write calls in source | Same as above | `expo.ios.infoPlist.NSHealthUpdateUsageDescription` |
| HealthKit capability | Entitlement `com.apple.developer.healthkit` + ASC capability | Certificates, Identifiers & Profiles → App ID; App Privacy | `.entitlements` files, `expo.ios.entitlements`, Xcode capability | INFERRED from entitlements | `app.json` → `expo.ios.entitlements` |
| Health data restrictions | No HealthKit data for advertising, marketing, data-mining, or sale to third parties; no sharing except to provide health services (or with explicit consent) | App Privacy questionnaire + review scrutiny | Scan for health data flowing into ad/analytics SDKs | If any ad/analytics SDK receives health-derived data → blocking HUMAN REQUIRED warning (Guideline 5.1.3 / 27.5) | — |

## 7. Product Page — Screenshots, Previews, Icon

Console: `My Apps → [App] → App Store → [Version] → <device> Display`. The public Product Page can be previewed before submission with Apple's Product Page Preview tool (developer.apple.com/app-store/product-page/).

### Screenshots

| Display class | Required? | Portrait px | Landscape px | Notes |
| --- | --- | --- | --- | --- |
| 6.9" iPhone | **Required** (current standard) | 1320 × 2868 | 2868 × 1320 | Modern requirement since iPhone 16 Pro Max; 6.9" set covers current iPhones |
| 6.5" iPhone | Legacy, optional | 1284 × 2778 | 2778 × 1284 | Still accepted for older-device coverage |
| 5.5" iPhone | Legacy, optional | 1242 × 2208 | 2208 × 1242 | Oldest accepted iPhone class |
| 13" iPad | **Required if the app runs on iPad** | 2064 × 2752 | 2752 × 2064 | Check `UIDeviceFamily` / `expo.ios.supportsTablet` / target device families in `project.pbxproj` |
| 12.9" iPad Pro | Legacy, optional | 2048 × 2732 | 2732 × 2048 | Older iPad class |

Rules: PNG or JPEG, sRGB or Display P3, no alpha/transparency, max 10 per display class, no placeholder or unfinished content (rejection risk). Derivation: screenshots are NOT in the repo by default — HUMAN REQUIRED (capture from real app screens); SUGGESTED only a shot list per key flow. If a design/fastlane `screenshots/` directory exists, cite it as INFERRED source material.

### App Previews and icon

| Field | Limits / format | Where in ASC | How to derive from repo | Status guidance | EAS / Expo mapping |
| --- | --- | --- | --- | --- | --- |
| App Preview video | Optional; 15–30 s per video, up to 3 per display class per locale | Version page → App Previews | Existing demo video URL/file in repo | HUMAN REQUIRED (produce footage); SUGGESTED shot list | — |
| App Icon | 1024 × 1024 PNG, no alpha channel, no rounded corners (Apple masks it), sRGB/Display P3 | Uploaded with the binary; shown on Product Page | `expo.icon` path; native `AppIcon.appiconset` in `Assets.xcassets` | INFERRED (cite the asset path); verify 1024×1024 and alpha absence — flag as blocking if the asset is missing or has transparency | `app.json` → `expo.icon` |

## 8. Export Compliance

Console: per-build question after upload; permanently skipped with the `Info.plist` key.

| Field | Limits / format | Where in ASC | How to derive from repo | Status guidance | EAS / Expo mapping |
| --- | --- | --- | --- | --- | --- |
| `ITSAppUsesNonExemptEncryption` | Boolean in `Info.plist` | Build processing → Export Compliance question | Scan deps for non-exempt crypto: custom algorithms, direct OpenSSL/libsodium usage beyond OS APIs. HTTPS/ATS, OS-provided crypto, and standard exempt uses → `false` | SUGGESTED from the dependency scan with rationale; final legal answer HUMAN REQUIRED. Non-exempt use requires an ERN/annual self-classification filing with US BIS | `app.json` → `expo.ios.config.usesNonExemptEncryption: false`; native: key in `Info.plist`. See [ios-app-store.md](../../eas-app-stores/references/ios-app-store.md) troubleshooting |

## 9. Age Rating Questionnaire

Console: `My Apps → [App] → App Information → Age Rating` (2025+ questionnaire producing tiers 4+, 9+, 13+, 16+, 18+). Answer honestly and consistently with the app's actual content and its target audience; mismatches cause rejection or re-rating. EAS Metadata `advisory` keys (full JSON shape in [app-store-metadata.md](../../eas-app-stores/references/app-store-metadata.md)) map 1:1 to these topics.

| Questionnaire topic | `advisory` key | Detect from repo | Status guidance |
| --- | --- | --- | --- |
| Profanity or crude humor | `profanityOrCrudeHumor` | UGC/chat features, content strings | SUGGESTED default `NONE`; content judgment HUMAN REQUIRED |
| Sexual content or nudity / graphic | `sexualContentOrNudity`, `sexualContentGraphicAndNudity` | Dating/adult features | HUMAN REQUIRED |
| Violence (cartoon/fantasy, realistic, prolonged-graphic) | `violenceCartoonOrFantasy`, `violenceRealistic`, `violenceRealisticProlongedGraphicOrSadistic` | Game/combat features | HUMAN REQUIRED for games; SUGGESTED `NONE` for utilities |
| Alcohol, tobacco, drug use or references | `alcoholTobaccoOrDrugUseOrReferences` | Feature scan | HUMAN REQUIRED |
| Simulated gambling | `gamblingSimulated` | Loot boxes, casino-style mini-games, card games with virtual chips | HUMAN REQUIRED — Apple scrutinizes loot-box mechanics |
| Real gambling | `gambling` (boolean) | Wagering/payment-to-play features; requires native app + geo-restriction | HUMAN REQUIRED |
| Contests | `contests` | Prize/contest features | HUMAN REQUIRED |
| Horror or fear themes | `horrorOrFearThemes` | Content scan | HUMAN REQUIRED |
| Mature or suggestive themes | `matureOrSuggestiveThemes` | Content scan | HUMAN REQUIRED |
| Medical or treatment information | `medicalOrTreatmentInformation` | Health symptom/diagnosis/treatment features (see §6) | SUGGESTED from health evidence; HUMAN REQUIRED |
| Unrestricted web access | `unrestrictedWebAccess` (boolean) | Open WebViews/browsers where users reach arbitrary web content | INFERRED from WebView evidence + HUMAN REQUIRED confirmation |
| User-generated content & moderation | (Kids-category and UGC questions) | Chat, comments, file sharing, moderation tooling presence | SUGGESTED from features; UGC without filtering/reporting/blocking is a rejection risk (Guideline 1.2) |
| Kids age band | `kidsAgeBand` (`FIVE_AND_UNDER`, `SIX_TO_EIGHT`, `NINE_TO_ELEVEN`) | Only when KIDS category chosen | HUMAN REQUIRED — never infer child-directed intent |

## 10. Info.plist Usage Descriptions (`NS*UsageDescription`)

Every privacy-sensitive API requires a purpose string shown in the system permission dialog. Apple rejects vague or boilerplate copy (Guideline 5.1.1). Drafting formula: **name the feature + name the data + state the user benefit** — e.g. "MyApp uses the camera to scan receipts so you can log expenses in one tap." Localize via `InfoPlist.strings`. Evidence: `Info.plist`, `expo.ios.infoPlist`, `app.config.ts` plugins.

| Key | Protects access to | Draft copy guidance | Detect from repo | EAS / Expo mapping |
| --- | --- | --- | --- | --- |
| `NSCameraUsageDescription` | Camera | Name the capture feature (scan, photo profile, video call) | `expo-camera`, `react-native-vision-camera`, `AVCaptureDevice` | `expo.ios.infoPlist.NSCameraUsageDescription` |
| `NSPhotoLibraryUsageDescription` | Photo library (read) | Name the picker/import feature | `expo-image-picker`, `PHPickerViewController` | `expo.ios.infoPlist.NSPhotoLibraryUsageDescription` |
| `NSPhotoLibraryAddUsageDescription` | Photo library (add-only) | Name the save/export feature (save edited image, export receipt) | Save-to-library calls | `expo.ios.infoPlist.NSPhotoLibraryAddUsageDescription` |
| `NSMicrophoneUsageDescription` | Microphone | Name the recording feature (voice notes, calls, video audio) | `expo-av`, `react-native-audio-api`, `AVAudioRecorder` | `expo.ios.infoPlist.NSMicrophoneUsageDescription` |
| `NSLocationWhenInUseUsageDescription` | Location while app is foreground | Name the location feature (nearby stores, GPS tagging) | `expo-location`, `CLLocationManager`, maps SDKs | `expo.ios.infoPlist.NSLocationWhenInUseUsageDescription` |
| `NSLocationAlwaysAndWhenInUseUsageDescription` (+ legacy `NSLocationAlwaysUsageDescription`) | Background location | Strong justification required; geofencing/fitness tracking only | `UIBackgroundModes: location`, background location APIs | `expo.ios.infoPlist` + `expo.ios.backgroundModes` |
| `NSContactsUsageDescription` | Contacts | Name the feature (invite friends, contact picker) | `expo-contacts`, `CNContactStore` | `expo.ios.infoPlist.NSContactsUsageDescription` |
| `NSCalendarsUsageDescription` (iOS 17+: `NSCalendarsFullAccessUsageDescription`, `NSCalendarsWriteOnlyAccessUsageDescription`) | Calendar read/write | Name the feature (event scheduling, reminders sync); write-only needs its own string on iOS 17+ | `EKEventStore`, calendar libs | `expo.ios.infoPlist.NSCalendars*UsageDescription` |
| `NSRemindersUsageDescription` (iOS 17+: `NSRemindersFullAccessUsageDescription`) | Reminders | Name the task/reminder feature | `EKEventStore` reminders, task libs | `expo.ios.infoPlist.NSRemindersUsageDescription` |
| `NSBluetoothAlwaysUsageDescription` (+ legacy `NSBluetoothPeripheralUsageDescription`) | Bluetooth | Name the device/feature (pairing with scale, headphones config) | `CoreBluetooth`, device-pairing libs | `expo.ios.infoPlist.NSBluetoothAlwaysUsageDescription` |
| `NSLocalNetworkUsageDescription` (+ `NSBonjourServices`) | Local network devices | Name the discovery feature (cast to TV, printer setup) | Bonjour/mDNS, Chromecast/AirPlay local discovery | `expo.ios.infoPlist.NSLocalNetworkUsageDescription` |
| `NSFaceIDUsageDescription` | Face ID | Name the unlock feature | `LAContext` with biometry, `expo-local-authentication` | `expo.ios.infoPlist.NSFaceIDUsageDescription` |
| `NSMotionUsageDescription` | Motion & fitness sensors | Name the step/activity feature | `CMMotionActivityManager`, pedometer libs | `expo.ios.infoPlist.NSMotionUsageDescription` |
| `NSSpeechRecognitionUsageDescription` | Speech recognition | Name the dictation/voice-command feature | `SFSpeechRecognizer`, voice input libs | `expo.ios.infoPlist.NSSpeechRecognitionUsageDescription` |
| `NSSiriUsageDescription` | Siri integration | Name the shortcut/intent feature | `INIntent`, App Intents, SiriKit | `expo.ios.infoPlist.NSSiriUsageDescription` |
| `NSUserTrackingUsageDescription` | ATT prompt (IDFA access) | Must explain tracking purpose honestly; required before `requestTrackingAuthorization` | `AppTrackingTransparency`, `react-native-tracking-transparency`, AdMob | `expo.ios.infoPlist.NSUserTrackingUsageDescription` |
| `NSHealthShareUsageDescription` / `NSHealthUpdateUsageDescription` | HealthKit read / write | See §6 — name the exact health data and benefit | HealthKit entitlement + source calls | `expo.ios.infoPlist.NSHealth*UsageDescription` |
| `NSAppleMusicUsageDescription` | Media library | Name the music-picker feature | `MPMediaPickerController` | `expo.ios.infoPlist.NSAppleMusicUsageDescription` |
| `NSHomeKitUsageDescription` | HomeKit | Name the smart-home feature | `HMHomeManager` | `expo.ios.infoPlist.NSHomeKitUsageDescription` |

Missing-key rule: if the code calls a protected API but the key is absent from `Info.plist`/`expo.ios.infoPlist`, flag it as a **blocking** finding (runtime crash + review rejection), status HUMAN REQUIRED with the exact key to add. Boilerplate rule: a key that exists only as Expo/plugin-injected generic English copy in the prebuilt `Info.plist` (e.g. a default `NSLocationWhenInUseUsageDescription`) is blocking-equivalent — Apple 5.1.1 rejects vague copy; draft feature-specific copy in the app's store language. Also audit injected keys with no corresponding feature usage and list them for removal (e.g. `NSLocationAlways*`, `NSMicrophoneUsageDescription` when unused).

## 11. EAS / Expo Mapping Summary

Only fill the artifact's EAS section when `eas.json` / Expo config is detected. Delivery paths (details in the `eas-app-stores` references, not duplicated here):

| Artifact content | EAS mechanism | Where configured |
| --- | --- | --- |
| Listing copy (name, subtitle, description, keywords, URLs, promo text, release notes) | EAS Metadata push (`eas metadata:push`) or manual ASC entry | `store.config.json` (`apple.info.<locale>.*`) — see [app-store-metadata.md](../../eas-app-stores/references/app-store-metadata.md) |
| Categories, copyright, advisory (age rating), release strategy, review contact | EAS Metadata | `store.config.json` (`apple.categories`, `apple.copyright`, `apple.advisory`, `apple.release`, `apple.review`) |
| Usage-description strings | Build-time injection | `app.json` → `expo.ios.infoPlist.*` |
| Bundle ID, version, build number, icon, entitlements | Build config | `app.json` → `expo.ios.*`; `eas.json` → `cli.appVersionSource`, `build.*.autoIncrement` |
| Submission identity (`ascAppId`, `appleId`, `appleTeamId`, ASC API key) | `eas submit` | `eas.json` → `submit.<profile>.ios` — see [ios-app-store.md](../../eas-app-stores/references/ios-app-store.md) |
| Programmatic metadata without EAS Metadata | App Store Connect API | `appStoreVersionLocalizations`, `appMediaScreenshots`, `appPrivacyDetails` resources |

Account IDs, API-key paths, and emails are always placeholders here and HUMAN REQUIRED in the artifact.
