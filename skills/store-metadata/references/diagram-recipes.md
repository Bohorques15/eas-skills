# Diagram Recipes — Optional archify Visual Companions (step 6)

Evidence→diagram recipes for the optional sidecar diagrams. **Soft dependency:** if the `archify` skill is installed, generate the diagrams below that have evidence; if it is absent — or no diagram has evidence — skip silently. The artifact is always complete alone; diagrams never block, delay, or gate it.

## Ownership, rendering, privacy

- **Archify owns the contract.** IR shape, JSON schemas, validation, repair receipts, rendering, and quality levels are defined by archify's own `SKILL.md` (plus its `schemas/README.md`). Read the matching type's schema and one example from archify's package, as its fast-authoring path directs. This file never duplicates its schemas — it only maps store-metadata evidence to diagram content.
- **Rendering pattern** (archify's `SKILL.md` is the authority for exact flags and quality levels):
  - validate: `node <archify-skill-dir>/bin/archify.mjs validate <type> <ir>.json`
  - deliver: `node <archify-skill-dir>/bin/archify.mjs deliver <type> <ir>.json <out>.html`
  - `<type>` is archify's: `dataflow` | `lifecycle` | `sequence` | `workflow` | `architecture`.
- **Offline:** set `ARCHIFY_UPDATE_CHECK_DISABLED=1` in the environment of every archify call so generation is fully offline.
- **Placeholder discipline applies to labels.** Node/edge/state labels obey the SKILL.md Hard Rules: never credential values, never invented URLs, emails, or account IDs; cite credential files by NAME only (e.g. `google-services.json`).
- **Scratch files:** write candidate `<ir>.json` outside the analyzed repo (agent temp dir). Only delivered HTML — plus archify's own delivery snapshots beside it — may land in `store-metadata/diagrams/`.

## Output convention

Deliver to `store-metadata/diagrams/{data-flow,lifecycle,review-sequence,release-workflow,permissions-architecture}.html` (alongside `index.html`). The artifact's Visual companions section (`#sec-visual-companions`) links each file relatively (`diagrams/NAME.html`) so `index.html` remains offline-viewable alone. Link only sections kept in THIS artifact (an iOS-only artifact keeps no Android card anchors, and vice versa).

## Priority when budget is limited

Generate top-down, and only diagrams with evidence:

1. Data Flow — personal-data collection is the highest-stakes store declaration.
2. Lifecycle — account/data deletion states (Apple/Google deletion requirements).
3. Sequence — reviewer access journey (sign-in required).
4. Workflow — build→release pipeline (EAS or manual).
5. Architecture — feature→permission justification.

## 1. Data Flow — `data-flow.html` (archify type `dataflow`)

- **When to generate:** any personal-data collection evidence from steps 1–2: auth/social login, payments/IAP, push notifications, analytics, ad SDKs, KYC/financial features.
- **Content mapping:**
  - Data-type nodes from the same evidence that fills the App Privacy (iOS checklist §5) / Data Safety (Android checklist §4) rows: contact info, identifiers, location, health, financial data, etc.
  - Destinations: the own backend versus each third-party SDK actually found in dependency manifests (FCM, payment provider, AdMob/AppLovin/Facebook Ads, analytics) — one node per SDK with a `file:line`-cited presence.
  - Sensitivity boundaries: group health/financial/children-related data as sensitive; mark flows the checklists classify as tracking or third-party sharing.
  - Edge labels carry the collection purpose, matching the purposes declared in the privacy/data-safety rows.
- **Linked from artifact sections:** `#sec-ios-app-privacy`, `#sec-android-data-safety`.
- **Skip when:** no personal-data collection evidence (e.g. fully offline utility app).

## 2. Lifecycle — `lifecycle.html` (archify type `lifecycle`)

- **When to generate:** account-creation evidence from steps 1–2 (auth flows, auth dependencies, session tokens).
- **Content mapping:**
  - Account/data states: create → use → deletion-request → actual-delete, per the deletion paths declared in App Privacy (iOS §5) and the Data Safety "Can users request deletion?" row (Android §4).
  - Flag mismatches: when repo evidence shows the delete endpoint only suspends the account, model deletion-request → suspended and mark it — that is a blocking HUMAN REQUIRED finding in the artifact rows too.
  - Terminal and failure states follow archify's lifecycle semantics (its schema owns the state types).
- **Linked from artifact sections:** `#sec-ios-app-privacy`, `#sec-android-data-safety`.
- **Skip when:** no account/auth evidence.

## 3. Sequence — `review-sequence.html` (archify type `sequence`)

- **When to generate:** sign-in is required for review — App Review Information (iOS §3) "Sign-in required" evidence, or App Access (Android §11) test-account rows.
- **Content mapping:**
  - Reviewer journey: app-open → demo-login → core-feature-reached, taken from the review notes / test-account instructions drafted in those rows.
  - Participants: reviewer, app, backend/auth service — only services evidenced in the repo.
  - Demo credentials appear as the same placeholders used in the artifact rows (`demo@example.com` / `REPLACE_WITH_DEMO_PASSWORD`) — never real or invented values.
- **Linked from artifact sections:** `#sec-ios-review-information`, `#sec-android-app-access`.
- **Skip when:** no auth wall for reviewers (sign-in not required).

## 4. Workflow — `release-workflow.html` (archify type `workflow`)

- **When to generate:** build/sign/release tooling evidence from steps 1–2: `eas.json`, CI configs, fastlane, gradle signing config, `store.config.json`.
- **Content mapping:**
  - Lanes/phases: build → sign → submit → review → release.
  - EAS steps (`eas build`, `eas submit`) when `eas.json` is detected; otherwise the manual equivalents (archive/bundle, console upload) — the same EAS-vs-manual split the artifact's EAS mapping section states.
  - Annotate which artifact metadata feeds each step: version/build number into build, export-compliance and demo-account rows into submit, pricing/track rows into release.
- **Linked from artifact sections:** `#eas-mapping-section`, `#sec-android-release-mechanics`.
- **Skip when:** no release-tooling evidence found (a generic store process without repo facts adds nothing).

## 5. Architecture — `permissions-architecture.html` (archify type `architecture`)

- **When to generate:** at least one `NS*UsageDescription` key (iOS §10) or dangerous/restricted Android permission (Android §12) found in evidence.
- **Content mapping:**
  - Feature→permission edges: each feature found in the source/dependency scan (camera, microphone, location, contacts, photos, health, push) linked to the permission or usage-description key that justifies it.
  - Trust boundary: device vs cloud — permissions gate on-device capabilities; show which features then send data off-device (cross-reference the Data Flow diagram instead of duplicating destinations).
  - Unjustified permission (no feature edge) and missing key (feature without permission) mirror the blocking flags in the artifact rows.
- **Linked from artifact sections:** `#sec-ios-infoplist-usage`, `#sec-android-permissions`.
- **Skip when:** no permission/usage-description evidence.

## Validation and failure handling

- Archify validates before deliver; a non-zero `deliver` is never success. On failure, follow archify's repair-receipt flow — change only the diagnosed subject and rerun — up to the correction rounds archify's own contract allows; stop at its stop rule, not a round count invented here.
- A diagram that cannot be validated/delivered is **skipped**: record it in the artifact's Visual companions table with status `skipped-not-validated` and NO link — never link a broken or missing file.
- If zero diagrams pass, delete the whole `#sec-visual-companions` section per the template's conditional rule and record the reason in the artifact footer.
- Diagrams not generated for lack of evidence simply get no table row; the footer deletion record covers the whole section only when it is deleted.
