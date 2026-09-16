# Agent-hybridization spec (apply per repo)

Goal: every repo readable identically by Claude Code, OpenAI Codex, and GitHub Copilot.
Canonical file: `AGENTS.md` at repo root. Everything else is a pointer.

## Templates

### T1 — CLAUDE.md stub (exact content, replaces any existing CLAUDE.md AFTER its content is migrated)

```
@AGENTS.md

<!-- Canonical agent instructions live in AGENTS.md (shared with Codex and GitHub Copilot). Edit AGENTS.md, never this file. -->
```

### T2 — .github/copilot-instructions.md pointer (only in repos that already have this file)

```
Follow the instructions in [AGENTS.md](../../AGENTS.md) at the repository root. AGENTS.md is the canonical instruction set for all coding agents — do not duplicate or add instructions here.
```

### T3 — Shared block appended at the END of every AGENTS.md (exact content)

```
## Cross-agent conventions

- This file (`AGENTS.md`) is the single source of truth for agent instructions in this repo. `CLAUDE.md` and `.github/copilot-instructions.md` are pointers to it — never edit them, never duplicate content into them.
- Shared repository skills live in `.agents/skills/` (one folder per skill with a `SKILL.md`); Codex scans this location natively. Keep any `.claude/skills/` compatibility bridge pointer-only or generated from this directory. New shared skills always go in `.agents/skills/`.
- Claude-specific subagent definitions live in `.claude/agents/`. If you are not Claude Code, you may read them as role/process guidance.
- Session continuity across tools: before ending substantial work in ANY tool (Claude Code, Codex, Copilot), record durable context — decisions made, gotchas discovered, in-progress state worth resuming — in the "Working notes" section below, or fold it into the relevant section above. This is the shared memory between agents.

## Working notes

<!-- Any agent: append short dated notes here (YYYY-MM-DD — note). Prune notes when stale or once folded into the sections above. -->
```

## Per-repo procedure (order matters)

1. `cd` into the repo. Run `git status --porcelain` and note pre-existing dirty files — you must NOT stage or revert them.
2. If behind origin (`git fetch` then check): `git pull --ff-only`. If ff-only fails, continue with local state but DO NOT push at the end; note it in your report.
3. Build `AGENTS.md`:
   - If AGENTS.md exists AND CLAUDE.md exists: merge CLAUDE.md's unique content into AGENTS.md (keep AGENTS.md structure; do not rewrite prose, move sections mechanically; drop exact duplicates).
   - If only CLAUDE.md exists: `git mv CLAUDE.md AGENTS.md` (or copy content if CLAUDE.md untracked).
   - If neither exists: create a MINIMAL AGENTS.md — title `# <repo> — Agent instructions`, then a 5–15 line factual project overview you derive from README/manifest/tree (what it is, language/stack, how to build/test/run if evident). Do not pad or invent.
   - If `.github/copilot-instructions.md` exists: move any content not already covered into AGENTS.md, then replace the file with template T2 exactly.
   - If a `.codex/` dir contains instruction-like markdown: merge unique content into AGENTS.md, leave the `.codex/` dir itself in place otherwise untouched.
   - Append template T3 at the end of AGENTS.md (skip if an identical "Cross-agent conventions" section already exists).
   - Near the top of AGENTS.md (right under the H1), add this one-liner if not present:
     `> Canonical instructions for all coding agents (Claude Code, Codex, GitHub Copilot). Codex reads this directly; Claude and GitHub Copilot use pointer files when present.`
4. Write CLAUDE.md with template T1 exactly (create it even if it never existed).
5. Skills layout:
   - Create `.agents/skills/` as the checked-in Codex-native repository skill directory.
   - Move shared skills from legacy `.claude/skills/` or another repository skill directory into `.agents/skills/` and update active references.
   - If Claude compatibility is needed, keep `.claude/skills/` as an untracked pointer, symlink, junction, or generated mirror of `.agents/skills/`; never maintain two independent sources.
6. Commit: stage ONLY the files this procedure touched (`git add AGENTS.md CLAUDE.md` plus pointer/symlink files as applicable; if you used `git mv` the rename is already staged). NEVER `git add -A` / `git add .`.
   - Do not change any git config. The repo's existing user.name/user.email must be used as-is.
   - Commit message:
     ```
     chore: hybridize agent instructions (AGENTS.md canonical)

     AGENTS.md is now the single source of truth for Claude Code, OpenAI
     Codex, and GitHub Copilot. CLAUDE.md and copilot-instructions.md are
     pointers; .agents/skills is the Codex-native canonical skill directory.

     Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>
     ```
7. Push: `git push origin <current-branch>`. If rejected (policy/permissions/non-ff), instead `git checkout -b chore/agent-hybridization && git push -u origin chore/agent-hybridization`, then `git checkout <original-branch>` — the commit must remain on the original branch too in that case? NO — simpler: if push to the branch is rejected, leave the commit on the local branch, push the same commit as branch `chore/agent-hybridization` (`git push origin HEAD:refs/heads/chore/agent-hybridization`), and report clearly.
8. Report per repo: what existed before, what you created/merged, commit hash + author identity used, push result (branch pushed / rejected / skipped and why), any anomalies.

## Hard rules
- Never touch, stage, revert, or stash pre-existing dirty files.
- Never modify git config, remotes, hooks, or credentials.
- Never force-push.
- Content migration is mechanical: preserve the user's prose; deduplicate only exact/near-exact overlaps; do not "improve" their instructions.
- homelab's CLAUDE.md is ~64KB: pure rename to AGENTS.md + append T3; do not restructure.
