Find the `store-metadata` skill — first existing path wins:

1. `skills/store-metadata/SKILL.md` (this pack repo)
2. `.claude/skills/store-metadata/SKILL.md`
3. `.codex/skills/store-metadata/SKILL.md`
4. `.opencode/skills/store-metadata/SKILL.md`
5. `.agents/skills/store-metadata/SKILL.md`
6. `~/.claude/skills/store-metadata/SKILL.md`
7. `~/.agents/skills/store-metadata/SKILL.md`

Read that `SKILL.md`, its `references/android-metadata-checklist.md`, and its `assets/artifact-template.html`, then execute the skill workflow for platform `android` against the current repository, writing the artifact to `store-metadata/index.html` (or the path given in the arguments).

Extra context (app-name hints, output path, locale): $ARGUMENTS

If none of the paths exists, stop and tell the user to install the skill first: `npx skills add Bohorques15/eas-skills --skill store-metadata` (see the pack README for manual-copy locations). In Codex the skill can also be invoked natively as `$store-metadata android` once installed.
