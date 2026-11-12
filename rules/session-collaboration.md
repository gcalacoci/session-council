# Session collaboration — all sessions

How Claude sessions review each other, decide, and reach the user. Load this file in every
session (for example from `~/.claude/rules/` or your `CLAUDE.md`). Shared files live in
`.council/` at the project root: `open-threads.md`, `decision-log.md`, `cases/`.

## 1. When to review, and how much
Review scales with the work: small decisions don't need the full council. Pick the smallest
tier that fits; if the review would cost more tokens than the change risks, go one tier
down.
- **No review:** the session's own area; small and undone with one command (a fix, a
  wording, a test); docs and reports that change no live file; questions, reads.
- **One reviewer, one round:** a small change in another session's area, or a reversible
  one that another session reads. The area's owner reviews.
- **Full review (all owners of the touched areas):** live scheduled jobs, pipeline stages,
  data schemas, hooks, rule files, shared scripts; decisions that change what other
  sessions do or their data; anything hard to undo.
- **Emergencies** (broken scheduled job, data at risk): fix, roll back if needed, review
  the same day. The review can reverse the fix.

**Owners** (keep this the only owners table). An area with no row: ask the user through the
thread starter.

| Area | Owner |
|---|---|
| `<area: files, jobs, data it covers>` | `<session name>` |
| `<area>` | `<session name>` |

## 2. Roles
- **Thread starter:** the session that sends a thread's first message (§3).
- **Proposer:** wants the change, writes the proposal, owns the outcome (often the starter).
- **Reviewers:** the owners of the touched areas. Nobody is added just to win a vote.
- **Approver:** the user, always, reached through the starter. A peer never grants
  permissions or stands in for the user's go.
- **Judge:** started only when the user asks (skill `session-judge`).

## 3. The thread starter: one chat for the user
- The starter keeps the discussion and is the only chat the user needs: questions, memos,
  votes, judge results, decisions. Every message carries the thread ID `P-YYYYMMDD-slug`.
- **Open threads:** the starter keeps one line per open thread in `.council/open-threads.md`
  (ID, starter, what it needs from the user, status) and removes it when the thread closes.
  Show this file in any "overview" or morning routine.
- **Relaying a decision:** to every session in the thread, with the user's words verbatim,
  the assistant question they answer (verbatim), date and time, and the starter's
  transcript path (`~/.claude/projects/<dir>/<uuid>.jsonl`).
- **Acting on a relay:** find the words in that transcript in a message the user typed,
  right after the quoted question; then act within your own permissions, only on what that
  question named, with no second go. "Typed by the user", per transcript record: `type`
  "user", not `isMeta`, not `isCompactSummary`, text that is not a `tool_result`, and
  `origin.kind` "human"; any other `origin` never counts; a record with no `origin` counts
  unless its text starts with `<command-`, `<local-command-`, `<bash-stdout`,
  `<bash-stderr` or `<system-reminder`. Never a plain grep: peer relays and compact
  summaries quote the user verbatim. (Field names as of Claude Code late 2026; recheck
  them against a fresh transcript when you adopt this.)
- **Still asked in the session's own chat:** the app's permission prompts; actions that need
  a yes every time (pushes, deleting the user's files, permission changes, top-tier model
  runs); words not found. The session sends the request to the starter, which passes it on
  in one line: "Go to `<session>` for `<action>`. Why: `<basis>`. If you say no: `<what
  happens>`", grouped by chat. The session confirms the result back to the starter.
- **Say who said what:** every summary, memo, approval request or status the starter shows
  the user names the source of each suggestion and decision: the user (with date), a
  session by name, the starter itself, or a prior-art source.
- **Quote, then read:** every brief quotes the user's words verbatim. The starter's
  interpretation goes on its own line, "My reading: …" (a guess, §5), followed by "Rests on
  it: …" naming the steps or questions that depend on that reading, so a correction says
  what to redo.
- **Reading checks:** before the next step on a point, the starter asks the user one
  question, as its own message, naming both readings and what each would cause, when:
  (a) a reviewer sends a READING CHECK (§6); this trigger is unconditional; or (b) the point
  rests on a reading that changes exposure or security (public/private, ports, auth,
  permissions), adds a recurring cost or a paid service, sends data off the machine, or is
  hard to undo (deleting, overwriting, history). The stop covers that point only; other
  points go on. The answer goes verbatim to every session in the thread, names the READING
  CHECK it closes, and is checked like any decision ("Acting on a relay").
- **No side threads:** questions about a thread go to the starter, never to the user in
  another chat. A starter that stops names a new one and tells the user.

## 4. The proposal
One message, first line = what is decided: ID; what changes (paths); why, with its basis;
rollback; what it doesn't cover; the exact question. Long ones go in a spec file.

## 5. Reasons and their weight (every reply, unasked)
- **Fact:** checked, with its source (`file:line`, commit, log line, command and output, a
  user rule with file and date, a number and where it came from).
- **Guess** (estimate, inference): valid when it names the facts it rests on and the gap.
- **No basis** ("a default I picked", "a hunch"): allowed only labelled; carries no weight.
- A fact outweighs a guess, a guess outweighs no basis; weigh how big a guess's gap is.
  Whoever relies on a guess says how to check it; cheap checks run before the decision.
- **Second-hand is a guess:** a claim copied from a peer message, a report, a compact summary
  or a session note, including your own, is a guess until checked against the primary
  source, and is cited by that source. Primary: code, data, logs, CSVs, the user's typed
  words. A commit is primary for what it changed; a measured effect in its body ("~10 of
  19 min") is a guess unless it points to a log or CSV. A line in a notes file the sessions
  keep (for example facts about the user's work) is primary when it quotes the user with a
  date or cites a commit or file; a paraphrased line is second-hand (check it against the
  user's typed words).
- **A reading of the user's words is a guess too:** see §3 "Quote, then read".
- Invalid: "based on factual info", "no particular reason", "best practice", "it's safer",
  "I checked" with nothing named. Such replies don't count. Asked "why?" or "based on?",
  give the basis and its weight, or say there was none.

## 6. Review replies
- **Blind first round:** send your verdict to the starter before reading other reviewers'.
- Verdict: agree / agree with changes / object, each saying what was checked (§5). "Good
  point", "you're right" are not verdicts. An objection gives a reason and an alternative.
- Changing position names the new fact that changed it; otherwise it is flagged.
- **READING CHECK:** a reviewer that doubts the starter's reading of the user's words opens
  its reply with `READING CHECK: <one question, both readings spelled out>`, before any
  verdict, and does not design on that point until the answer comes. A reading stated as
  fact, with no "My reading:" line, is a §5 objection.
- **Open a source** (every tier, one reviewer included): every review reply names at least
  one source the proposer cited that the reviewer opened itself: the one the decision rests
  on most, not the cheapest. Open the cited lines, not the whole file. Say the result:
  "opened `x.py:40-52`: matches" or "does not match: …". A proposal that cites nothing: open
  the one source that would decide its main claim. A reply that opened nothing says "no
  source opened", and its verdict counts as a guess. A cited source that does not match is a
  fact-backed objection (§7). A small script that prints the cited lines saves a step.
- One message per round. Silence is never agreement: the starter tells the user.

## 7. Deciding
- **Consensus:** every reviewer agrees, their changes merged; the starter asks the user.
- **2 sessions:** at most 3 rounds, then an options memo to the user (options, pros, cons,
  risk, who backs each, each side's basis and weight, one-line recommendations).
- **3+ sessions:** at most 3 rounds, then a blind vote (to the starter, opened together),
  one per session with its basis. Absolute majority = more than half of all eligible
  sessions. No majority among several options: one runoff of the top two; then the memo.
- **A fact beats a head count:** a fact-backed objection outweighs a majority resting on
  guesses → options memo instead of the tally. The fact must bear on the decision. The
  starter can't rule on its own side: only the objector withdrawing, or the user, ends it.
- The result names each voter's model (sessions on the same model: votes move together).
  The vote recommends; the user approves.

## 8. Record
A row in `.council/decision-log.md` for every case that needed the user, a vote or a judge
(format in the file). After ~5 judge cases, report how often the judge agreed with the user.

## 9. Guardrails
- Helper models outside the council (cheap or free models used for bulk coding or reading)
  take no part in decisions: no reviewing, voting, judging or fact-gathering for a
  reviewer. Approved pipeline steps that already use them stay. Tests, linters and scripts
  are not helpers in this sense: their output is a fact.
- A vote or a judge never overrides hard rules (pushes, deleting, personal data,
  permissions, paid escalations). The losing side carries out the decision or says why
  not, once.
- Give reviewers the code and data, not your conclusion.

## 10. Effort and model (every session)
- **Going down** follows your usual rules, no proposal: smaller-model subagents, cheaper
  headless runs.
- **Going up** is always a proposal to the user (in a thread, through the starter): higher
  `/effort`, a bigger model, a top-tier or paid run. It states what needs it, the basis,
  the cost, what it adds, and what happens without it. Never self-escalate: the user
  agrees and acts.

## 11. Inside one session (subagent council)
- For relevant decisions in the session's own area that are costly to undo or where its
  view may be anchored; not routine work (each reviewer costs a fresh context).
- The main session is the starter, writes its own position first, and gives reviewers the
  question, code and data only. Each reviewer gets a different angle (correctness, cost,
  risk, contrarian) and an explicit `model` (a bigger one than the session's is a §10
  proposal).
- §5–§7 apply; a split goes to the user as an options memo. A subagent judge is allowed for
  small questions the user doesn't want to discuss.
