---
name: hybridize-repo
description: Use when a repo (new or existing) should share the same instructions, skills, and working memory across Claude Code, OpenAI Codex, and GitHub Copilot — sets up AGENTS.md as the canonical instruction file with pointers for each tool. Trigger on "hybridize this repo", "set up agent instructions", "make this repo cross-agent", or right after creating/cloning a new repo.
---

# Hybridize a repo (AGENTS.md canonical)

Goal: the repo reads identically in Claude Code, OpenAI Codex, and GitHub Copilot. `AGENTS.md` at the repo root is the single source of truth; everything else points at it. The procedure is idempotent — safe to re-run.

## Layout produced

| File | Role |
|---|---|
| `AGENTS.md` | Canonical instructions (all three tools read it natively) |
| `CLAUDE.md` | 2-line stub: `@AGENTS.md` import (see templates/CLAUDE.md.stub) |
| `.github/copilot-instructions.md` | Thin pointer, only if the file already existed (templates/copilot-instructions.md) |
| `.agents/skills/` | Canonical repository-local skills directory scanned by Codex |
| `.claude/skills/` | Optional pointer, symlink, junction, or generated compatibility mirror |

## Procedure

1. `git status --porcelain` — note pre-existing dirty files; never stage, revert, or modify them. If behind origin, `git pull --ff-only` first (if that fails, do the work but skip the push and say so).
2. Build `AGENTS.md`:
   - CLAUDE.md with real content → move it into AGENTS.md (`git mv` if AGENTS.md absent; merge unique content mechanically if both exist — never rewrite the user's prose).
   - `.github/copilot-instructions.md` with real content → migrate unique content into AGENTS.md, then replace the file with the pointer template.
   - Neither exists → write a minimal AGENTS.md: `# <repo> — Agent instructions`, then a 5–15 line factual overview from README/manifest/tree. Don't pad, don't invent.
   - Under the H1 add: `> Canonical instructions for all coding agents (Claude Code, Codex, GitHub Copilot). Codex reads this directly; Claude and GitHub Copilot use pointer files when present.`
   - Append the cross-agent block (templates/cross-agent-block.md) at the end, unless already present.
3. Write `CLAUDE.md` from templates/CLAUDE.md.stub (always, even if none existed).
4. Create or preserve `.agents/skills/` as the checked-in Codex-native repository skill directory. Move legacy `.claude/skills/` content into it. If Claude compatibility is needed, keep `.claude/skills/` only as a pointer, symlink, junction, or generated mirror of `.agents/skills/`; never maintain two independent sources.
5. Commit ONLY the touched files (never `git add -A`). Respect the repo's existing git identity — never change git config. Message: `chore: hybridize agent instructions (AGENTS.md canonical)`.
6. Push to the current branch; if rejected by branch policy, push the commit as `chore/agent-hybridization` and tell the user a PR is needed.

## Rules

- The user's prose is sacred: move it, dedupe exact overlaps, never "improve" it.
- Working notes section in AGENTS.md is the cross-tool memory: every agent (any tool) records durable decisions and resumable state there; prune stale notes when folding them into the main sections.
- Never touch dirty files, git config, remotes, hooks, or credentials. Never force-push.
