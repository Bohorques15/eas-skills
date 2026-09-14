# Google Play Console Metadata Checklist (Android)

Console-form checklist for publishing an Android app to Google Play Console. One row per field: limits, exact console location, how to derive the value from the repository, status guidance, and the EAS/Expo mapping. Fill the results into `../assets/artifact-template.html` following `../SKILL.md`.

**Status rules.** `INFERRED` requires a `file:line` citation from the analyzed repo. `SUGGESTED` requires a rationale. `HUMAN REQUIRED` states the exact decision the developer must make. Never invent URLs, account IDs, demo credentials, or legal claims — use a clearly-marked placeholder (e.g. `https://example.com/privacy-policy`) flagged HUMAN REQUIRED.

**Console path convention.** Paths reflect the 2025 Play Console layout: listings under `Grow → Store presence`, policy declarations under `Policy → App content`, releases under `Release → [track]`, pricing under `Monetize → Pricing and distribution`. Google renames nav items periodically; when a label differs, search the field name in the Play Console search bar.

**EAS-flow depth (not duplicated here).** If the `eas-app-stores` skill is installed alongside this one, see [play-store.md](../../eas-app-stores/references/play-store.md) for service-account setup, `eas submit`, tracks, and `releaseStatus` semantics. Note: EAS Metadata is Apple-only (preview) — Android listing fields are entered manually in Play Console or via the Google Play Developer API. The iOS counterpart of Data Safety is App Privacy — see [ios-metadata-checklist.md](ios-metadata-checklist.md).

## 1. Main Store Listing

Console: `Grow → Store presence → Main store listing`.

| Field | Limits / format | Where in Play Console | How to derive from repo | Status guidance | EAS / Expo mapping |
| --- | --- | --- | --- | --- | --- |
| App name | 30 chars | Main store listing → App name | `expo.name`; `android:label` in `AndroidManifest.xml`; `app_name` in `res/values/strings.xml` | INFERRED when found; SUGGESTED brand+keyword variant for ASO | `app.json` → `expo.name` (becomes the label at prebuild) |
| Short description | 80 chars | Main store listing → Short description | Draft from README/value proposition. Play has no Subtitle field — the short description fills that role | SUGGESTED with rationale | — (Play API `listings.shortDescription`) |
| Full description | 4000 chars | Main store listing → Full description | Draft from README, features, onboarding copy | SUGGESTED; performance/social-proof claims HUMAN REQUIRED (never fabricate) | — (Play API `listings.fullDescription`) |
| Package name | `applicationId`, reverse-DNS, **immutable** after the app is created | Chosen in the Create app dialog; cannot change later | `expo.android.package`; `applicationId` in `build.gradle(.kts)`; `package` attr in `AndroidManifest.xml` | INFERRED (cite file); flag mismatches between config and gradle | `app.json` → `expo.android.package` |
| Default language | Affects listing fallback | Create app dialog / Store settings | Default `res/values/strings.xml` locale, i18n config | SUGGESTED | — |
| App icon | 512 × 512 PNG, 32-bit with alpha | Main store listing → App icon | `expo.icon`; `android/app/src/main/res/mipmap-*` launcher icons; adaptive icon layers | INFERRED (cite asset path); verify a 512×512 export exists — HUMAN REQUIRED if only adaptive/mipmap layers are present | `app.json` → `expo.icon`, `expo.android.adaptiveIcon` |
| Feature graphic | 1024 × 500 PNG/JPG, no text-dependent safe zones issues | Main store listing → Feature graphic | Not in repo by default | HUMAN REQUIRED (design asset); SUGGESTED composition notes | — |
| Phone screenshots | Min 2, max 8; 16:9 or 9:16 aspect; each side 320–3840 px | Main store listing → Phone screenshots | Not in repo by default; check fastlane `metadata/android` screenshot dirs | HUMAN REQUIRED (capture from real app); INFERRED source material when a screenshots dir exists; SUGGESTED shot list | — |
| 7" tablet screenshots | Optional, recommended if tablets supported | Main store listing → Tablet (7") | Tablet support: `supports-screens`, resource qualifiers (`sw600dp`), `expo.android` config | SUGGESTED requirement note; assets HUMAN REQUIRED | — |
| 10" tablet screenshots | Optional, recommended if tablets supported | Main store listing → Tablet (10") | Same tablet-support signals | Same as 7" | — |
| Promo video | YouTube URL, optional | Main store listing → Promo video | Search repo/README for an existing video URL | INFERRED only if a real YouTube URL exists; otherwise optional + HUMAN REQUIRED | — |

**ASO note.** Google indexes the app name, short description, and terms repeated 2–5× in the full description — there is no separate Keywords field on Google Play (the iOS Keywords field has no Android equivalent). Work target keywords naturally into the copy; keyword stuffing is penalized.

## 2. Categorization and Content Rating (IARC)

| Field | Limits / format | Where in Play Console | How to derive from repo | Status guidance | EAS / Expo mapping |
| --- | --- | --- | --- | --- | --- |
| App or game category | Single category (e.g. Productivity, Finance, Health & Fitness; games pick a genre) | Main store listing → Category | Infer from features/deps | SUGGESTED from evidence; HUMAN REQUIRED to confirm | — |
| Tags | Up to 5 free-form tags | Main store listing → Tags | Derive from feature vocabulary | SUGGESTED | — |
| Content rating (IARC questionnaire) | Produces 3+, 7+, 12+, 16+, 18+ | `Policy → App content → Content rating` | Scan for: violence, sexual content, profanity, substance references, gambling mechanics, in-app purchases, UGC/chat, web access | Each answer SUGGESTED-from-evidence with rationale or HUMAN REQUIRED; re-submit the questionnaire after content changes (ratings can be revoked for mismatches) | — |

IARC questionnaire topics to answer per category: cartoon/fantasy vs realistic violence; sexual content/nudity; profanity/crude humor; alcohol/tobacco/drug references; real or simulated gambling (incl. loot boxes); presence of in-app purchases; user interaction/UGC sharing (chat, file sharing → affects rating and Families eligibility); unrestricted web browsing.

## 3. Contact Details and Privacy Policy

Console: `Grow → Store presence → Contact details` and `Policy → App content → Privacy policy`.

| Field | Limits / format | Where in Play Console | How to derive from repo | Status guidance | EAS / Expo mapping |
| --- | --- | --- | --- | --- | --- |
| Support email | Required, shown publicly | Contact details → Email | Generic team address in README/`package.json` author may hint | HUMAN REQUIRED — placeholder `support@example.com` | — |
| Support phone | Optional | Contact details → Phone | Not derivable | HUMAN REQUIRED / optional | — |
| Support website | Optional public URL (the Play equivalent of the iOS Support URL) | Contact details → Website | Search repo for support/help URLs | INFERRED if a real URL exists; otherwise placeholder + HUMAN REQUIRED | — |
| Privacy policy URL | **Mandatory** when the app handles personal or sensitive user data (permissions, accounts, analytics effectively always trigger this); public, stable URL | `Policy → App content → Privacy policy` | Search repo/README for a published policy URL | INFERRED only if a real URL exists; otherwise placeholder + HUMAN REQUIRED (must be authored/hosted; must match Data Safety answers) | — |

## 4. Data Safety Form

Console: `Policy → App content → Data safety`. Declares what user data the app **collects** (leaves the device, incl. to your own servers) vs **shares** (to third parties), how it is handled, and how users control it. Answers MUST be consistent with the permissions and SDKs actually found in the repo. Wrong declarations violate the User Data policy.

### Google data-type taxonomy and repo detection

| Data type category | Included data types | Detect from repo (evidence to cite) |
| --- | --- | --- |
| Location | Approximate location, Precise location | `ACCESS_COARSE/FINE_LOCATION`, `expo-location`, `react-native-maps`, fused-location APIs, IP geolocation |
| Personal info | Name, Email address, User IDs, Address, Phone number, Other info | Auth SDKs (Firebase Auth, Google Sign-In), account/profile screens |
| Financial info | Purchase history, Budget history, Credit score, Other financial info | Play Billing (`com.android.billingclient`), `react-native-iap`, payment SDKs (Stripe), fintech features |
| Health and fitness | Health data, Fitness data | Health Connect (`androidx.health.connect`), `BODY_SENSORS`, step/workout features (see §10) |
| Messages | Emails, SMS/MMS, In-app messages | SMS permissions, chat features, messaging SDKs |
| Photos and videos | Photos, Videos | `READ_MEDIA_IMAGES/VIDEO`, camera/gallery features |
| Audio files | Audio files | `RECORD_AUDIO`, voice notes, audio playback libs |
| Files and docs | Files and docs | `READ_EXTERNAL_STORAGE`/`MANAGE_EXTERNAL_STORAGE`, document pickers, file upload features |
| Calendar | Calendar events | `READ/WRITE_CALENDAR` |
| Contacts | Contacts | `READ_CONTACTS`, invite/social features |
| App activity | In-app search history, Installed apps, Other user-generated content, Other actions | Analytics events, search persistence, `QUERY_ALL_PACKAGES`, UGC features |
| App info and performance | Crash logs, Diagnostics, Other app performance data | Crashlytics, Sentry, Bugsnag, ANR/perf SDKs |
| Device or other IDs | Device ID, Advertising ID, other IDs | AD_ID permission (see §5), analytics SDKs, install referrer, attribution SDKs |
| Web browsing | Web browsing history | WebViews where visited URLs are collected/reported (not mere embedding) |

### Per-data-type questionnaire inputs

For each declared data type, the form asks:

| Question | Answer guidance |
| --- | --- |
| Collected and/or shared? | Shared = data leaves the app to a third-party SDK/service (ad networks, analytics providers). Third-party SDKs count even if your code never touches the data |
| Required or optional? | Required = app can't function without it; optional = user can decline and still use core features. Match actual runtime behavior (permission gating) |
| Processing purposes | App functionality, Analytics, Account management, Developer advertising, Advertising and marketing, Fraud prevention/security/compliance, Personalization. Select only evidenced purposes |
| Encrypted in transit? | Check `networkSecurityConfig`, HTTPS-only endpoints, TLS usage. Answer YES only with evidence; otherwise HUMAN REQUIRED |
| Can users request deletion? | Requires a real in-app or web deletion path. If absent → blocking HUMAN REQUIRED (Play requires a data-deletion mechanism for collected data) |

**Third-party sharing watchlist (ad/analytics SDKs).** AdMob/Google Mobile Ads, AppLovin, Meta Audience Network, Unity Ads, IronSource → advertising data sharing. Firebase Analytics/GA4, Amplitude, Mixpanel, Segment → analytics sharing. Crashlytics/Sentry → diagnostics sharing. Every SDK found in `build.gradle`, `Podfile`, or `package.json` that phones home must appear in the form.

## 5. Advertising ID

| Item | Requirement | Detect from repo | Status guidance |
| --- | --- | --- | --- |
| `com.google.android.gms.permission.AD_ID` permission | Auto-merged into the manifest by `play-services-ads` and related Google SDKs when `targetSdk ≥ 33` | Grep manifest for `AD_ID`; check deps for `play-services-ads`, `react-native-google-mobile-ads`, AdMob config plugins | INFERRED from manifest/deps |
| Play advertising-ID policy | When users opt out of Ads Personalization, the advertising ID must return zeros; using it despite opt-out violates policy and the User Data policy | Ad SDK config (`requestConfiguration` / tag-for-child-directed settings), consent flows (UMP SDK) | SUGGESTED compliance notes; consent-flow presence INFERRED or blocking HUMAN REQUIRED |
| Data Safety consistency | If AD_ID is present, declare **Device or other IDs** in §4 and answer the ads-related questions accordingly | Derived from the two rows above | Consistency rule — never declare "no data" with ad SDKs installed |
| No-ads alternative | Remove the permission via `tools:node="remove"` in the manifest or avoid SDKs that merge it; then declare no advertising ID use | Manifest merger directives | SUGGESTED when no ad SDK exists; INFERRED when a removal directive is present |
| EAS mapping | `expo.android.permissions` includes/removals; AdMob config plugin in `app.json` `plugins` | — | — |

## 6. Target Audience and Content

Console: `Policy → App content → Target audience and content`.

| Field | Limits / format | Where in Play Console | How to derive from repo | Status guidance | EAS / Expo mapping |
| --- | --- | --- | --- | --- | --- |
| Target age groups | Multi-select: 0–5, 6–8, 9–12, 13–15, 16–17, 18+ | Target audience and content → Age groups | Content scan (kids UI, cartoon styling, subject matter) — weak signal only | HUMAN REQUIRED business decision; SUGGESTED default 18+ for utility apps with no child-directed evidence | — |
| Contains child-directed content (under 13) | Triggers the **Google Play Families Policy** | Same page | Kids-targeted features/content | HUMAN REQUIRED — never infer child-directed intent from code alone | — |
| Families Policy consequences | When children are targeted: ads must be non-personalized only (no advertising-ID-based personalization), only from Google-certified ad SDKs in self-certified mode; content/UGC restrictions; COPPA/GDPR-K-compliant privacy policy; **Teacher Approved** badge eligibility | Policy enforcement, not a single field | Ad SDK presence + child targeting = conflict | If child-directed AND personalized ads detected → blocking HUMAN REQUIRED warning | — |
| Content rating consequence | Target audience answers feed the IARC rating (§2); mismatches cause downgrades/removal | Derived | — | Keep §2 and §6 answers consistent | — |

## 7. Ads Declaration

Console: `Policy → App content → Ads`.

| Field | Limits / format | Where in Play Console | How to derive from repo | Status guidance | EAS / Expo mapping |
| --- | --- | --- | --- | --- | --- |
| Contains ads | Yes / No — "ads" means any ad shown in or through the app | Ads → Contains ads | Detect SDKs: AdMob (`play-services-ads`, `react-native-google-mobile-ads`, `expo-ads-admob` legacy), AppLovin MAX, Unity Ads, Meta/Facebook Audience Network, IronSource, Vungle, Mintegral | INFERRED from dependency evidence (cite manifest/gradle/package.json lines); misdeclaration violates policy — if evidence is ambiguous, HUMAN REQUIRED | `app.json` → `plugins` (ad SDK config plugins), `expo.android` deps |

## 8. Financial Features Declaration

Console: `Policy → App content → Financial features` (nav label varies; Google's Financial Services policy governs).

| Field | Limits / format | Where in Play Console | How to derive from repo | Status guidance | EAS / Expo mapping |
| --- | --- | --- | --- | --- | --- |
| Financial functionality declaration | Declare if the app offers financial products: loans (APR + terms disclosure, licensing), payments/money transmission, crypto exchange/wallets, insurance, investing/brokerage, banking | Financial features declaration | Scan features/deps: lending flows, wallets, exchange integrations, KYC flows. A plain Stripe checkout for physical goods is NOT a financial feature | SUGGESTED detection from evidence; the declaration itself + jurisdictional licensing are HUMAN REQUIRED (legal claims must never be drafted by the agent) | — |
| Compliance requirements | Region-specific: e.g. US personal-loan APR disclosure, India digital-lending declarations, licensed-entity verification | Enforced via the declaration form | — | HUMAN REQUIRED with pointer to Google's Financial Services policy | — |

## 9. Government Apps

| Field | Limits / format | Where in Play Console | How to derive from repo | Status guidance | EAS / Expo mapping |
| --- | --- | --- | --- | --- | --- |
| Government-app declaration | Apps representing a government entity must be published by an authorized representative; Google requires verification (authorization letters, domain/organization proof) | App-content verification flow (see Play Help "Government apps") | Signals only: org naming, `.gov` domains, official-brand assets in repo | HUMAN REQUIRED always — the agent cannot verify authority; if government signals are detected, raise it as a blocking decision with a pointer to Play's Government apps policy | — |

## 10. Health Apps (Play Policy)

| Field | Limits / format | Where in Play Console | How to derive from repo | Status guidance | EAS / Expo mapping |
| --- | --- | --- | --- | --- | --- |
| Health-apps declaration | Required when the app accesses Health Connect or handles health/personal-health data: comply with the Health apps policy — privacy policy must disclose health data handling, no ads personalization from health data, no sale/sharing of health data for ads, data-deletion path required | `Policy → App content → Health apps` | `androidx.health.connect` deps, `READ/WRITE_HEALTH_CONNECT_PERMISSIONS`, `BODY_SENSORS`, health-record features | INFERRED from dependency/permission evidence; policy compliance steps HUMAN REQUIRED | `app.json` → `expo.android.permissions`, Health Connect config plugins |
| Restricted health categories | Diagnosis/treatment/device-companion claims may require additional review or are restricted | Policy enforcement | Symptom-checker/diagnostic features | HUMAN REQUIRED — flag any medical-claim copy found in the repo | — |

## 11. App Access (Reviewer Login) and Managed Publishing

Console: `Policy → App content → App access`.

| Field | Limits / format | Where in Play Console | How to derive from repo | Status guidance | EAS / Expo mapping |
| --- | --- | --- | --- | --- | --- |
| Access required? | "All or some functionality requires access" when login, permissions, or hardware gate review | App access | Detect auth flows (login screens, auth deps), region locks, pairing/hardware requirements | INFERRED from auth evidence; otherwise SUGGESTED | — |
| Test-account instructions | Working credentials + step-by-step for the Google review team | App access → instructions | NEVER derive or invent credentials | HUMAN REQUIRED — developer must create a demo/test account with seeded data. Placeholder `reviewer-demo@example.com` / `REPLACE_WITH_TEST_PASSWORD` | — |
| Managed publishing notes | With managed publishing ON (Release section → Publishing overview), releases stay pending until manually published — reviewers and rollout timing behave differently; state whether it is on | `Release → Publishing overview` | Not derivable | HUMAN REQUIRED; note in the artifact so the developer doesn't mistake "pending" for "rejected" | — |

## 12. AndroidManifest Permissions

Evidence sources: `AndroidManifest.xml` (`uses-permission`, `uses-feature`), `expo.android.permissions`, `app.json` plugins, config-plugin manifests, `build.gradle` deps. Every dangerous permission triggers a runtime prompt (API 23+) and a Play policy question; every restricted permission may require a declaration form.

### 12a. Dangerous (runtime) permissions

| Permission | Protects | User-facing rationale copy (suggestion) | Detect from repo | EAS / Expo mapping |
| --- | --- | --- | --- | --- |
| `CAMERA` | Camera | "Scan receipts and documents to log them instantly" | `expo-camera`, `react-native-vision-camera`, `CameraX` | `expo.android.permissions`, camera plugins |
| `RECORD_AUDIO` | Microphone | "Record voice notes and dictate entries hands-free" | `expo-av`, `MediaRecorder`, audio libs | `expo.android.permissions` |
| `ACCESS_FINE_LOCATION` / `ACCESS_COARSE_LOCATION` | Precise / approximate location | "Show nearby stores and tag entries with your location" | `expo-location`, `react-native-maps`, `FusedLocationProvider` | `expo.android.permissions` + location plugins |
| `ACCESS_BACKGROUND_LOCATION` | Location while backgrounded | Requires separate incremental prompt + Play declaration; only for core background features | `UIBackgroundModes`-equivalent services, foreground location services | HUMAN REQUIRED if detected — high policy risk |
| `READ_CONTACTS` (+ `WRITE_CONTACTS`, `GET_ACCOUNTS`) | Contacts / accounts | "Find friends already using MyApp to invite them" | `expo-contacts`, `ContactsContract` | `expo.android.permissions` |
| `READ_CALENDAR` / `WRITE_CALENDAR` | Calendar | "Add your booked sessions to your calendar automatically" | `CalendarContract`, calendar libs | `expo.android.permissions` |
| `READ_EXTERNAL_STORAGE` / `WRITE_EXTERNAL_STORAGE` | Legacy shared storage (cap with `maxSdkVersion`; superseded by media permissions on API 33+) | "Import photos and documents you choose" | Storage libs, legacy file pickers | `expo.android.permissions` (Expo prebuild already scopes these) |
| `READ_MEDIA_IMAGES` / `READ_MEDIA_VIDEO` / `READ_MEDIA_AUDIO` | Media files on API 33+ (replaces READ_EXTERNAL_STORAGE) | "Pick photos/videos to attach to your entries" | Photo pickers, media libs with modern targeting | `expo.android.permissions` |
| `POST_NOTIFICATIONS` | Notifications on API 33+ (runtime prompt) | "Get reminders so you never miss an entry" | `expo-notifications`, `firebase-messaging`, `NotificationManager` | `expo.android.permissions`, notifications plugin |
| `BLUETOOTH_SCAN` / `BLUETOOTH_CONNECT` / `BLUETOOTH_ADVERTISE` | Bluetooth on API 31+ (legacy `BLUETOOTH`/`BLUETOOTH_ADMIN` need `maxSdkVersion=30`); add `android:usesPermissionFlags="neverForLocation"` when not deriving location | "Pair with your scale to sync readings automatically" | `BluetoothAdapter`, BLE libs, device pairing | `expo.android.permissions` |
| `NEARBY_WIFI_DEVICES` (API 33+, `neverForLocation` flag when applicable) / `ACCESS_WIFI_STATE` + `CHANGE_WIFI_STATE` | Wi-Fi scanning and nearby-device discovery | "Connect to your printer over the local network" | Cast/print/local-device libs | `expo.android.permissions` |
| `BODY_SENSORS` (+ `BODY_SENSORS_BACKGROUND`) | Biometric sensors (heart rate etc.) | "Read heart-rate data during workouts" | Health/fitness libs | `expo.android.permissions`; see §10 policy |
| `READ_PHONE_STATE` | Telephony state (often merged by SDKs) | Usually SDK-driven; justify only if the app truly needs call-state awareness | SDK manifest merging | Audit whether it can be removed |

### 12b. Restricted permissions requiring a Play declaration form

| Permission(s) | Policy gate | Detect from repo | Status guidance |
| --- | --- | --- | --- |
| `QUERY_ALL_PACKAGES` | Package-visibility declaration; prefer scoped `<queries>` intents instead | Manifest + any installed-apps listing feature | If present, flag SUGGESTED removal + `<queries>` alternative; declaration form HUMAN REQUIRED |
| `MANAGE_EXTERNAL_STORAGE` | All-files-access declaration; prefer SAF/MediaStore | File-manager features, legacy storage code | High-risk; justify or remove — HUMAN REQUIRED |
| SMS / Call log group: `READ_SMS`, `RECEIVE_SMS`, `SEND_SMS`, `RECEIVE_MMS`, `RECEIVE_WAP_PUSH`, `READ_CALL_LOG`, `WRITE_CALL_LOG`, `PROCESS_OUTGOING_CALLS` | Allowed only for default SMS/phone handler apps; declaration form required | SMS-verification or dialer features | If present without default-handler role → blocking warning; HUMAN REQUIRED |
| `SCHEDULE_EXACT_ALARM` / `USE_EXACT_ALARM` | Exact-alarm declaration (API 31+/33+); `USE_EXACT_ALARM` only for core calendar/alarm apps | Alarm/reminder features, `AlarmManager.setExact*` | SUGGESTED inexact alternatives unless the feature is core; HUMAN REQUIRED |
| `FOREGROUND_SERVICE` + `FOREGROUND_SERVICE_<type>` (API 34+: `dataSync`, `mediaPlayback`, `phoneCall`, `location`, `camera`, `microphone`, `health`, `remoteMessaging`, `connectedDevice`, `mediaProjection`, `mediaProcessing`, `shortService`, `specialUse`, `systemExempted`) | `foregroundServiceType` in the manifest service must match the granted type permission; several types require justification | Services with `foregroundServiceType`, background tasks | INFERRED type list from manifest; justification text SUGGESTED per type |
| Other sensitive: `SYSTEM_ALERT_WINDOW`, `REQUEST_INSTALL_PACKAGES`, `WRITE_SETTINGS` | Case-by-case policy review | Overlay/sideload features | HUMAN REQUIRED with policy pointer |

## 13. Pricing, Distribution, and Release Mechanics

Console: `Monetize → Pricing and distribution`, `Release → [track]`.

| Field | Limits / format | Where in Play Console | How to derive from repo | Status guidance | EAS / Expo mapping |
| --- | --- | --- | --- | --- | --- |
| Free or paid | Set at first release; paid→free allowed, free→paid **not** | Pricing and distribution | IAP/billing deps suggest free + IAP | HUMAN REQUIRED business decision; SUGGESTED default Free when Play Billing is present | — |
| Countries / availability | Distribution selection | Pricing and distribution | Localization coverage hints at markets | HUMAN REQUIRED | — |
| App bundle format | **AAB required for new apps**; APK only for legacy additions | `Release → [track] → Create new release` | `eas.json` `buildType: app-bundle`; gradle `bundleRelease` | INFERRED from build config; APK-only setups → blocking warning | `eas.json` → `build.<profile>.android.buildType: "app-bundle"` |
| `versionName` | User-facing, e.g. `1.2.3` | Shown on the release/store page | `expo.version`; `defaultConfig.versionName` in `build.gradle` | INFERRED | `app.json` → `expo.version` |
| `versionCode` | Integer; must increase on every upload | Release metadata | `expo.android.versionCode`; `defaultConfig.versionCode` | INFERRED; note when EAS remote versioning owns it | `app.json` → `expo.android.versionCode`; `eas.json` → `cli.appVersionSource`, `autoIncrement` |
| Target API level | Must meet Play's current target-API policy for new apps and updates | Enforced at upload | `targetSdk`/`compileSdk` in `build.gradle`; Expo SDK version | INFERRED; flag if below the current policy requirement | Expo SDK upgrades manage this |
| Tracks | `internal` (≤100 testers) → `closed` → `open` → `production` | `Release → Internal/Closed/Open testing/Production` | Release process docs in repo | SUGGESTED promotion plan; setup steps in [play-store.md](../../eas-app-stores/references/play-store.md) (not duplicated) | `eas.json` → `submit.<profile>.android.track` |
| Staged rollout | Percentage of production users; pausable/haltable | Production release → Staged rollout | — | HUMAN REQUIRED rollout strategy (SUGGESTED conservative start, e.g. 10%) | `eas.json` → `submit.<profile>.android.releaseStatus: "inProgress"`, `rollout` |
| Play App Signing | Required for new apps; upload key vs Google-managed signing key | `Release → Setup → App signing` | `eas credentials -p android` state, keystore references in gradle | INFERRED setup hints; key material itself HUMAN REQUIRED (never commit keystores) | EAS manages upload keys — see [play-store.md](../../eas-app-stores/references/play-store.md) |

## 14. EAS / Expo Mapping Summary

Only fill the artifact's EAS section when `eas.json` / Expo config is detected.

| Artifact content | EAS mechanism | Where configured |
| --- | --- | --- |
| Listing copy (name, descriptions, graphics) | Manual Play Console entry or Google Play Developer API (EAS Metadata is Apple-only) | Play Console; `listings` API resources |
| Package name, versionCode, permissions, adaptive icon | Build config | `app.json` → `expo.android.*`; `expo.android.permissions`; config plugins |
| Push/FCM setup | `google-services.json` wiring | `app.json` → `expo.android.googleServicesFile` (+ `expo-notifications` plugin) |
| Submission auth | Play service-account JSON key — never committed; `google-service-account.json` paths in examples are placeholders | `eas.json` → `submit.<profile>.android.serviceAccountKeyPath` (older docs/templates call this the `googleServicesAccount`; some setups use `EXPO_ANDROID_SERVICE_ACCOUNT_KEY_BASE64` or `@secret:` refs) |
| Track and rollout | `eas submit` | `eas.json` → `submit.<profile>.android.track`, `releaseStatus` (`completed`, `draft`, `inProgress` + `rollout`, `halted`) — semantics in [play-store.md](../../eas-app-stores/references/play-store.md) |
| AAB build | EAS Build | `eas.json` → `build.<profile>.android.buildType: "app-bundle"` |
