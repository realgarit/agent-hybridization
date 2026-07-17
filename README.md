# agent-hybridization

Reusable scheme + skill for making a repo read identically across Claude Code, OpenAI Codex, and GitHub Copilot.

## The scheme

- `AGENTS.md` at the repo root is the **canonical** instruction file for all three tools (Claude Code, Codex, and Copilot all read it natively).
- `CLAUDE.md` is a 2-line stub that imports `AGENTS.md` (`@AGENTS.md`) — Claude Code loads it but the real content lives in AGENTS.md.
- `.github/copilot-instructions.md`, if present, is replaced with a thin pointer back to `AGENTS.md`.
- `.claude/skills/` is the canonical home for reusable skills (Copilot reads this directory natively); `.agents/skills` is a relative symlink to it so Codex sees the same skills.
- A "Working notes" section at the end of `AGENTS.md` is cross-tool memory — any agent, in any tool, records durable decisions and resumable state there so the next agent (in any tool) picks up context.

Full spec: [`docs/spec.md`](docs/spec.md). Skill implementation: [`skills/hybridize-repo/SKILL.md`](skills/hybridize-repo/SKILL.md).

## Install the skill globally

Symlink it into both tool-specific skill directories so it's available everywhere and updates to this repo propagate automatically:

```sh
mkdir -p ~/.claude/skills && ln -s /Users/realgar/Git/agent-hybridization/skills/hybridize-repo ~/.claude/skills/hybridize-repo
mkdir -p ~/.agents/skills && ln -s /Users/realgar/Git/agent-hybridization/skills/hybridize-repo ~/.agents/skills/hybridize-repo
```

## Use it

In any repo, in any tool (Claude Code, Codex, Copilot), say:

> hybridize this repo

The skill sets up `AGENTS.md`, `CLAUDE.md`, the copilot pointer (if applicable), and the `.agents/skills` symlink (if applicable) per the procedure in `docs/spec.md`.
