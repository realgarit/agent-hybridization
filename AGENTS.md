# agent-hybridization — Agent instructions

> Canonical instructions for all coding agents (Claude Code, Codex, GitHub Copilot). Codex reads this directly; Claude and GitHub Copilot use pointer files when present.

This repo holds the reusable "repo-hybridization" scheme and its skill implementation: it makes other repos read identically across Claude Code, OpenAI Codex, and GitHub Copilot by making `AGENTS.md` the canonical instruction file.

- `README.md` — what the scheme is and how to install/use the skill.
- `docs/spec.md` — the full spec this skill implements.
- `skills/hybridize-repo/SKILL.md` — the package skill source (installed into the user Codex and Claude skill locations when needed).
- `templates/` — the exact template files (`CLAUDE.md.stub`, `copilot-instructions.md`, `cross-agent-block.md`) referenced by the skill.

No build/test/run steps — this is a documentation + skill-definition repo, not a program.

## Cross-agent conventions

- This file (`AGENTS.md`) is the single source of truth for agent instructions in this repo. `CLAUDE.md` and `.github/copilot-instructions.md` are pointers to it — never edit them, never duplicate content into them.
- Shared repository skills live in `.agents/skills/` (one folder per skill with a `SKILL.md`). Codex scans this location natively. Keep any `.claude/skills/` compatibility bridge pointer-only or generated from this directory; never maintain two independent sources. New shared skills always go in `.agents/skills/`.
- Claude-specific subagent definitions live in `.claude/agents/`. If you are not Claude Code, you may read them as role/process guidance.
- Session continuity across tools: before ending substantial work in ANY tool (Claude Code, Codex, Copilot), record durable context — decisions made, gotchas discovered, in-progress state worth resuming — in the "Working notes" section below, or fold it into the relevant section above. This is the shared memory between agents.

## Working notes

<!-- Any agent: append short dated notes here (YYYY-MM-DD — note). Prune notes when stale or once folded into the sections above. -->

- 2026-09-16 — Codex-first layout sweep: repository-local shared skills use `.agents/skills/` as the canonical source. Any `.claude/skills/` path is only a compatibility bridge or generated mirror.
