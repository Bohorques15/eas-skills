# EAS App Stores skill pack

Agent skill for building and submitting iOS and Android apps with EAS (TestFlight, App Store, Google Play). Works with Expo and other React Native projects, plus existing native apps.

This pack is **derived from** the MIT-licensed [Expo `eas-app-stores` skill](https://github.com/expo/skills/blob/main/plugins/expo/skills/eas-app-stores/SKILL.md). It is **not** the official Expo plugin (`expo@claude-plugins-official` / `npx skills add expo/skills`).

> **EAS service — costs apply.** Cloud builds use EAS plan resources. Apple Developer and Google Play memberships are separate. See [expo.dev/pricing](https://expo.dev/pricing). Examples in the skill use placeholder emails, passwords, and `google-service-account.json` paths — do not commit real credentials.

## Install

Use the [skills CLI](https://github.com/vercel-labs/skills). Do not pipe remote scripts into a shell.

```sh
npx skills add Bohorques15/eas-skills --skill eas-app-stores
```

From a local clone of this repo:

```sh
npx skills add /path/to/eas-skills --skill eas-app-stores
```

Then restart (or reopen) the AI assistant and ask it to use `eas-app-stores` for store builds and submissions.

Repo: [Bohorques15/eas-skills](https://github.com/Bohorques15/eas-skills)

### Manual copy

Copy `skills/eas-app-stores` (the whole folder) into the location your assistant reads:

| Assistant | Project-local | User-wide |
| --- | --- | --- |
| Cursor | `.agents/skills/eas-app-stores` or `.cursor/skills/eas-app-stores` | `~/.cursor/skills/eas-app-stores` |
| Claude Code | `.claude/skills/eas-app-stores` | `~/.claude/skills/eas-app-stores` |
| Codex | `.codex/skills/eas-app-stores` | — |
| OpenCode | `.opencode/skills/eas-app-stores` or `.agents/skills/eas-app-stores` | — |

### Official Expo pack

If you want the full Expo skill set instead of this fork:

```sh
npx skills add expo/skills
```

See [Expo Skills docs](https://docs.expo.dev/skills/).

## store-metadata skill

`store-metadata` analyzes your REAL app repository (Expo, React Native, or native Swift/Kotlin) and generates ONE self-contained artifact, `store-metadata/index.html`, with every field App Store Connect (iOS) or Google Play Console (Android) requires to publish: listing copy, screenshot/icon specs, App Privacy and Data Safety answers, permission rationale copy, review/test-account info, age and content ratings, pricing, and release settings. Every value is marked **INFERRED** (with `file:line` evidence), **SUGGESTED** (with rationale), or **HUMAN REQUIRED** (with the exact decision needed). EAS is optional: the artifact is complete for fully manual submission, and adds `eas.json` mappings when the repo uses EAS. Unlike `eas-app-stores`, this skill is **original work of this pack** (MIT), not derived from Expo. It never modifies the analyzed repo — the artifact is the only file written.

### Commands

| Runtime | iOS | Android | Enable by |
| --- | --- | --- | --- |
| Claude Code | `/ios-metadata` | `/android-metadata` | Copy `.claude/commands/*.md` into your project (installing the skill alone also registers `/store-metadata`) |
| OpenCode | `/ios-metadata` | `/android-metadata` | Copy `.opencode/commands/*.md` into your project |
| Codex | `$store-metadata ios` | `$store-metadata android` | Nothing — native invocation once the skill is installed. Optional: copy `skills/store-metadata/prompts/codex/*.md` to `~/.codex/prompts/` for `/ios-metadata` and `/android-metadata` slash UX (a repo-committed `.codex/prompts/` is not loaded by Codex) |

Arguments after the command are passed through: platform override, app-name hints, output path, and target locale (e.g. `/ios-metadata MyApp out/index.html es-MX`).

### Install

```sh
npx skills add Bohorques15/eas-skills --skill store-metadata
```

Manual copy works like `eas-app-stores`: copy the whole `skills/store-metadata` folder to `.agents/skills/store-metadata`, `.claude/skills/store-metadata`, `.codex/skills/store-metadata`, `.opencode/skills/store-metadata`, or the user-wide equivalents (`~/.claude/skills/`, `~/.codex/skills/`, `~/.agents/skills/`, `~/.cursor/skills/`).

### Output artifact

`store-metadata/index.html` (at the analyzed repo root, or a path you pass) opens in any browser with no network access: sticky summary header, a "Blocking decisions" list, one table per console screen with per-value Copy buttons, collapsible long content, light/dark themes, and a print stylesheet so you can export it to PDF and use it as the submission checklist.

### Visual companions (optional)

When the **archify** skill is installed alongside this pack, `store-metadata` can also generate sidecar diagrams into `store-metadata/diagrams/` and link them from the artifact's "Visual companions" section. This is a soft dependency: archify absent, no evidence for a diagram, or a diagram that fails archify validation → it is silently skipped and the artifact stays complete on its own. Archify is MIT-licensed like this pack, so the licenses are compatible.

| Diagram | Generated when | Supports artifact sections |
| --- | --- | --- |
| `data-flow.html` | personal-data collection evidence (auth, payments, push, analytics) | App Privacy · Data Safety |
| `lifecycle.html` | account-creation evidence (create → use → deletion states) | App Privacy · Data Safety |
| `review-sequence.html` | reviewer sign-in / demo account needed | App Review Information · App Access |
| `release-workflow.html` | build/sign/release tooling (`eas.json`, CI, fastlane, gradle signing) | EAS Mapping · Release Mechanics |
| `permissions-architecture.html` | usage-description keys / dangerous permissions found | Info.plist Usage · AndroidManifest Permissions |

```sh
npx skills add tt-a1i/archify -g
```

For fully offline generation, set `ARCHIFY_UPDATE_CHECK_DISABLED=1`. Diagram links are relative, so `index.html` remains offline-viewable alone. Recipes: `skills/store-metadata/references/diagram-recipes.md`.

## Layout

```text
skills/eas-app-stores/
  SKILL.md
  agents/openai.yaml
  references/
skills/store-metadata/
  SKILL.md
  agents/openai.yaml
  assets/artifact-template.html
  prompts/codex/
  references/
.claude/commands/       # /ios-metadata, /android-metadata (Claude Code)
.opencode/commands/     # /ios-metadata, /android-metadata (OpenCode)
```

## License

MIT — see [LICENSE](LICENSE). Upstream Expo Skills are also MIT.
