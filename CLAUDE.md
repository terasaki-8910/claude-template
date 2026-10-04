# Project: <name>

## What this is
<one-paragraph purpose>. Fill this in through conversation with Claude, not by hand:
describe the project in one line, let Claude ask clarifying questions, then have it
write the settled description back into this section. Everything else below is
standing template.

## Workflow
No pipeline, no gated stages, no scripts/run.sh -- this is a plain conversation with
Claude: describe what you want, iterate, ask Claude to build it. Use Plan Mode
(Shift+Tab, or ask Claude to enter it) before anything non-trivial gets built.

For independent work -- multiple features/modules that don't share files and don't
depend on each other's output -- ask Claude to fan them out with the Workflow tool
(parallel subagents, isolated per-feature git worktrees) instead of building them one
at a time, then merge each back with `git merge --no-ff` once it looks right. Don't
parallelize work that shares files or has a real dependency order; that stays a normal
sequential conversation.

On the FIRST conversation in a new project (the section above still reads
`<one-paragraph purpose>`), Claude should also propose per-project tools (MCP servers /
plugins / skills) suited to what's being built -- once. I approve what's useful and
install it myself; Claude never auto-installs or enables tools. Once "What this is"
holds a real description, don't re-propose tools in later sessions unless the project's
scope changes substantially.

## Decisions
Choices that are mine to make -- which reading of an ambiguous request, anything I will
see, scope, open tradeoffs, conflicting instructions -- go through a decision sheet before
anything is built. The procedure (written in Japanese) is imported below. If you copied it
to `~/.claude/rules/decision-sheet.md` to use it in every project, delete the import line
so it doesn't load twice.
@docs/decision-sheet.md

## Plan Mode
For a genuinely hard architecture/design call, switch to Fable manually before entering
Plan Mode (`/model fable`), then switch back afterward (`/model sonnet` or
`/model default`). Claude Code has no hook or setting that binds a specific model to
Plan Mode automatically, so this is a manual step every time -- see
`~/.claude/CLAUDE.md` > Models.

## Model / effort indicator
The terminal status line shows the current model (and reasoning effort, where Claude
Code exposes it) -- see `.claude/statusline.sh`. It is not repeated at the end of every
chat reply.

## Language
Generated artifacts (code, comments, docs, commit messages, UI copy) default to English.
Change here to override per project. The language I chat in is separate and unaffected.

## Commands
- Tests: <e.g. npm test / pytest>
- Lint:  <e.g. npm run lint>   (MUST include: no-emoji, design-tokens-only, a11y)

## UI rules
IMPORTANT: colors ONLY via design tokens; never hardcode hex. No emoji in UI or source.
Shared personal UI direction: @~/.claude/rules/ui.md

## Stack recipes
If the chosen stack matches a doc under docs/recipes/ (e.g. docs/recipes/
tauri-desktop-app.md for a Tauri + pnpm desktop app), read it early and follow its
documented patterns/gotchas -- each one there cost real debugging time on a prior
project, not guessed in advance. If personal shared-infrastructure notes exist at
~/.claude/rules/infra.md (e.g. a self-hosted DB server reused across projects), check
there too -- it stays out of this repo, never committed, since this template is public.

## Do not touch
Design tokens (change only with my explicit approval), auto-generated files.

## Git
Local only by default; do NOT push unless asked -- `git push` is allow-listed in
`.claude/settings.json` (no confirmation popup) purely to remove friction once asked;
it does not change the underlying policy. Feature branches merge to main with --no-ff.
Commit and merge locally as work lands (see `~/.claude/CLAUDE.md` > Git) -- per-feature
git worktree isolation is the safety boundary for parallel work, not permission prompts.
