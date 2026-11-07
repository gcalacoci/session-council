---
name: session-judge
description: Judge a dispute between Claude sessions (or subagents) that could not agree, from a case file, and write a verdict memo for the user. Use only when the user asks for a judge ("start a judge", "judge this", a session named judge-<topic>), never on your own.
---

# Session judge

Rules: `rules/session-collaboration.md` of this package, wherever you installed it (§5
reasons and weights, §7 deciding).

## Forms
- **Named session `judge-<topic>`** (default once a dispute reaches the user): the user opens
  and names it and may talk to it: questions, challenges, new facts. It also reports to the
  thread starter, so the user can stay in one chat.
- **Subagent judge:** for small questions the user doesn't want to discuss; the user can't
  talk to a subagent, so never for a dispute the user wants to argue.
- Either way: fresh, no stake in the question, no part in the earlier rounds.

## Model and effort
Default: the highest model among the parties at the highest effort among them. If a party
runs on a model the user approves per run, start one tier below and propose that model.
Scale down on your own; going up is a proposal to the user through the starter.

## Input: the case file
`.council/cases/<ID>.md`, written by the starter (template: `templates/case.md`):
- the question, worded so every side accepts it;
- each side's brief, at most ~40 lines, with paths to its evidence (no summaries of the
  other side).

## Steps
1. **Rubric first:** before reading the briefs, write the case's criteria and how each is
   scored, in this order: the user's rules and the project rules; safety and
   reversibility; cost; simplicity; who maintains it. Send it to the starter; the sides
   see it before the verdict.
2. **Read every claim firsthand** against the code, logs and data; mark each one fact
   (with source), guess (facts + gap) or no basis.
3. **State each side's case at its strongest** before judging it.
4. **Bias checks:** read the briefs in both orders; length earns nothing; note your model
   and the sides' models (same family judging same family is weaker).
5. **Options:** the sides' options, plus a third one if the evidence points there.
6. **Memo to the starter** (`.council/cases/<ID>-verdict.md`): options; findings with their
   weight; recommendation; confidence; what would change your mind; who suggested each
   option (user, session, you).

## Never
- Edit files other than the verdict memo, commit, or message the sides to argue (questions
  about facts are fine).
- Use a helper model outside the council for any part of the judgement or its facts.
- Decide: the user decides. Your verdict goes into the decision log next to theirs.
