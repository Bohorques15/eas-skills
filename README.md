# EAS App Stores skill pack

Agent skill for building and submitting iOS and Android apps with EAS (TestFlight, App Store, Google Play). Works with Expo and other React Native projects, plus existing native apps.

This pack is **derived from** the MIT-licensed [Expo `eas-app-stores` skill](https://github.com/expo/skills/blob/main/plugins/expo/skills/eas-app-stores/SKILL.md). It is **not** the official Expo plugin (`expo@claude-plugins-official` / `npx skills add expo/skills`).

> **EAS service — costs apply.** Cloud builds use EAS plan resources. Apple Developer and Google Play memberships are separate. See [expo.dev/pricing](https://expo.dev/pricing). Examples in the skill use placeholder emails, passwords, and `google-service-account.json` paths — do not commit real credentials.

## Install

Use the [skills CLI](https://github.com/vercel-labs/skills). Do not pipe remote scripts into a shell.

After this repo is on GitHub:

```sh
npx skills add <owner>/<repo> --skill eas-app-stores
```

From a local clone or this folder:

```sh
npx skills add /path/to/EAS\ Skills --skill eas-app-stores
```

Then restart (or reopen) the AI assistant and ask it to use `eas-app-stores` for store builds and submissions.

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

## Layout

```text
skills/eas-app-stores/
  SKILL.md
  agents/openai.yaml
  references/
```

## License

MIT — see [LICENSE](LICENSE). Upstream Expo Skills are also MIT.
