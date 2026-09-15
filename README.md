# AZbox skill for coding agents

An [Agent Skill](https://agentskills.io) that teaches your coding agent how to integrate
[AZbox](https://azbox.io) translations into a project: the Flutter SDK, translation files
with `azbox-cli`, the REST API, CI, and how to add and look up translation keys without
inventing APIs that do not exist.

It is a plain `SKILL.md`, so it works in Claude Code, Cursor, Codex, Gemini CLI and any
agent that reads the Agent Skills format.

## Install

**Any agent** (auto-detects the agents you have installed):

```bash
npx skills add azbox-io/azbox-skills
```

**Claude Code**, as a plugin:

```
/plugin marketplace add azbox-io/azbox-skills
/plugin install azbox@azbox
```

**Cursor, Codex and others, by hand**: copy `skills/azbox/` into `.cursor/skills/`,
`.agents/skills/` or `.claude/skills/` in your project (Cursor reads all three).

## Use

Ask your agent things like:

- "Localize the settings screen with AZbox"
- "Set up AZbox in this Flutter app"
- "Pull the Spanish and French translations into locales/ in CI"

To let the agent read your project's keys directly, also add the
[AZbox MCP server](https://azbox.io/docs/api/mcp/).

Documentation: https://azbox.io/docs/api/skills/

## Licence

MIT
