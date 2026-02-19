# Familiar Cowork Skill

Standalone skill repository for Claude Code / Corework Cloud plugin installation.

## Repo Structure

- `.claude-plugin/plugin.json`: plugin manifest.
- `.claude-plugin/marketplace.json`: marketplace manifest so the repo can be added directly.
- `skills/familiar/SKILL.md`: Familiar skill definition.

## Publish

1. Create a new GitHub repository for this folder (for example `familiar-software/familiar-cowork-skill`).
2. Push the contents of this folder as the repository root.
3. Keep `plugin.json` `repository` aligned with the final GitHub URL.

## Install from Claude Code / Corework

```bash
/plugin marketplace add familiar-software/familiar-cowork-skill
/plugin install familiar@familiar-software
```

After installation, start a new session and ask the agent to use the `familiar` skill.
