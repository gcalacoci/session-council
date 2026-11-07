# session-council

Rules and a judge skill for several Claude Code sessions working on the same project:
who reviews what, how they argue (reasons with weights, blind rounds), how they decide
(consensus, vote, "a fact beats a head count"), and how the user stays in one chat.

## What's inside
| Path | What |
|---|---|
| `rules/session-collaboration.md` | the method: review tiers, owners table, thread starter, reasons and weights, review replies, deciding, record, guardrails, effort, subagent council |
| `skills/session-judge/SKILL.md` | a judge for disputes the sessions could not settle; runs only when the user asks |
| `templates/open-threads.md` | one line per open thread; the user's single view |
| `templates/decision-log.md` | one row per case that needed the user, a vote or a judge |
| `templates/case.md` | the case file a judge reads |
| `docs/example.md` | a worked example: one thread from proposal to close, a changed position, a relayed approval |
| `docs/design.md` | why each main choice was made, and the prior art it borrows from |
| `docs/faq.md` | cost per tier, two or one sessions, what depends on Claude Code internals, limits |

## Install
1. Rule, loaded by every session: copy `rules/session-collaboration.md` to
   `~/.claude/rules/` (all projects) or reference it from the project's `CLAUDE.md`.
2. Skill: copy or symlink `skills/session-judge` into `~/.claude/skills/` (or the
   project's `.claude/skills/`).
3. Shared files: at the project root, `mkdir -p .council/cases`, then copy
   `templates/open-threads.md` and `templates/decision-log.md` into `.council/`.
4. Fill the owners table in §1 with your sessions and their areas.
5. Name your sessions (`/rename`) so they can message each other by name.

## Requires
Claude Code with sessions that can message each other (`SendMessage` / `ListAgents`).
§3's "typed by the user" check reads Claude Code transcript fields; check them against a
fresh transcript before relying on it.

## Status
In use since November 2026 on one personal project with three or more long-running sessions.
