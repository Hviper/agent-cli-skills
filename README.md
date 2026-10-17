# Agent CLI Skills

A focused collection of skills for delegating work to local AI coding CLIs and coordinating multi-agent engineering tasks.

## Included skills

- `claude-cli` — direct Claude Code CLI delegation with resumable native sessions.
- `codex-cli` — direct Codex CLI delegation with JSON output and verification workflows.
- `antigravity-cli` — direct Google Antigravity CLI delegation and conversation recovery.
- `orchestrator-cli` — durable task orchestration across Claude, Codex, and Antigravity using GitHub Issues or local records.
- `codex-agent-manager` — lets Codex select Waza workflows, diagnose problems, and delegate implementation to Antigravity.

## Installation

Copy any skill directory into your agent skills directory:

```bash
cp -R claude-cli ~/.agents/skills/
```

Read each `SKILL.md` for provider-specific usage, safety boundaries, recovery procedures, and command references.

## License

MIT
