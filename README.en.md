<div align="right">

[![日本語](https://img.shields.io/badge/lang-%E6%97%A5%E6%9C%AC%E8%AA%9E-bc002d?style=flat-square)](./README.md)

</div>

# claude-template

A **GitHub template repository** for driving ONE project with Claude Code through **plain
conversation** -- no gated scripts. There is no `pipeline.yaml` and no `scripts/run.sh`.
`CLAUDE.md` itself is the project's living spec, and independent work fans out through the
Workflow tool and merges back in, instead of a scripted build stage. Multiple projects just
run in parallel as separate repos (no shared state).

If a project actually needs gated stages, unattended runs, or a scripted repair loop, start
from the sibling repo `claude-pipeline-template` instead (see "Relationship to
claude-pipeline-template" below).

## Files
- `CLAUDE.md` -- project memory: "What this is" (the project's purpose), workflow policy,
  the Plan Mode / Fable note, UI rules, and where to find stack recipes. This is effectively
  the only file you touch (its "What this is" section is meant to be filled in by Claude,
  through the first conversation, not by hand).
- `.claude/settings.json` -- scoped permissions + status line config (see below). Not global
  skip-permissions.
- `.claude/statusline.sh` -- the status line script. Prefers `jq`; without it, falls back to
  a plain-text scan for the model name only (effort is omitted rather than guessed when it
  can't be read reliably).
- `docs/ui-rules.starter.md` -- starter content for `~/.claude/rules/ui.md` (your shared,
  cross-project UI direction file). Copy it once and refine it over time; it isn't specific
  to this project.
- `docs/recipes/*.md` -- stack-specific known gotchas (e.g. a Tauri + pnpm desktop app).
  Claude reads the matching one early once the stack is chosen.

## One-time GLOBAL setup (per machine, NOT in this repo)
1. Stop the git co-author trailer everywhere:
   `~/.claude/settings.json` -> `{ "includeCoAuthoredBy": false }`
2. Shared UI direction across all projects: copy `docs/ui-rules.starter.md` to
   `~/.claude/rules/ui.md` and refine it over time. `~/.claude/CLAUDE.md` and
   `~/.claude/rules/` load in every project.

## Per-project use
1. On GitHub: make this a Template repository (Settings -> Template repository).
2. For each new project: "Use this template" -> new repo -> clone.
3. Start Claude Code in the cloned directory and just talk -- a one-line rough description
   is enough. You don't need to fill in `CLAUDE.md` by hand first: Claude asks clarifying
   questions, then writes the settled description back into "What this is" itself.
4. During that same first conversation, Claude also proposes per-project tools (MCP /
   plugins / skills) suited to what you're building -- once (triggered by "What this is"
   still being the unfilled placeholder). You decide what's worth it and install it
   yourself; Claude never auto-installs.
5. For a non-trivial architecture/design call, use Plan Mode (Shift+Tab). For a genuinely
   hard one, switch to Fable manually first (`/model fable`), then switch back afterward --
   Claude Code has no mechanism to bind a specific model to Plan Mode automatically, so this
   stays a manual step every time (see `CLAUDE.md` > Plan Mode).
6. For independent work -- multiple pieces that don't share files and have no dependency
   order -- ask Claude to fan them out with the Workflow tool (parallel subagents, per-
   feature git worktrees) and merge each with `--no-ff` once it looks right. Work that
   shares files or has a real dependency order stays a normal sequential conversation.
7. Nothing is pushed unless you explicitly ask. Everything stays in local git.

## Model / effort status line
`.claude/statusline.sh` shows the current model (reliably available) and, when Claude Code
exposes it, the reasoning effort, as an always-on terminal bar. Whether effort actually
appears in the status-line JSON is version-dependent and unconfirmed, so the script omits it
rather than guessing. It is not repeated at the end of every chat reply.

## Relationship to claude-pipeline-template
This repo has no `pipeline.yaml`, `scripts/run.sh`, `scripts/gates.sh`, `prompts/`, gated
stages, or the unattended `run.sh auto` / repair-loop machinery. A project that genuinely
needs test-gated unattended runs or per-stage machine checks should start from
`claude-pipeline-template` instead.
