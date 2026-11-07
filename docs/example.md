# Worked example: one thread, start to finish

Three long-running sessions share a project:
- `infra`: owns pipeline stages and the rule files;
- `triage`: owns the nightly queue script;
- `kit`: owns a starter kit.

The example is based on a real thread, with names and details changed.

## 1. The proposal (§4)
`infra` wants a new nightly stage. The nightly script belongs to `triage`, so this is a
change in another session's area: one reviewer, one round (§1). `infra` is the thread
starter. It adds a line to `.council/open-threads.md`:

```
| P-20261107-sidelists | infra | nothing | built; waiting on triage's review |
```

Then it sends the proposal:

> **P-20261107-sidelists, one-reviewer tier: please add one line to `queue_tail` in your
> file `nightly.sh`, or object.**
> What: after the `stage db_compare` line, add
> `stage sidelists || echo "sidelists: failed (nothing else depends on it)"`.
> Why (facts): the user said "go" today (spec §7: "stage sidelists in queue_tail; triage
> reviews"). The stage is registered in `stages.py` (commit 04d294d), max 300 s. Manual
> run today: rc 0, test suite 573 OK. It reads the queue and writes only `~/sidelists/`.
> Rollback: delete the line. Not covered: anything else in `queue_tail`.
> Question: agree / agree with changes / object?

The first line says what is decided. Every reason names its source.

## 2. The review reply (§6)
`triage` checks the claims itself before replying:

> **P-20261107-sidelists: agree. Committed in 0b32f1d.**
> What I checked:
> - the user's "go": a typed record in infra's transcript at 09:21, after the design
>   messages;
> - stages.py:67 has the registry row; the docs page has the stage row;
> - `queue_tail` has no `set -e`, and the `||` keeps a failure contained;
> - `bash -n` passes; suite 573 OK.
>
> Change I added: the schedule row on the docs page now lists the new stage, in the same
> commit.

"Agree" here comes with what was checked. "Looks good" would not count as a verdict.

## 3. Closing
`infra` checks commit 0b32f1d, removes the line from `open-threads.md`, and tells the user
in one message who did what: "triage reviewed and committed the line; it also updated the
schedule row". No vote, no judge, and the user typed nothing beyond the first "go".

## A harder case: a position that changed
In a full-review thread about these same rules, `infra` proposed a filter for "messages
the user typed": records with no `origin` field. Round 1, `triage` objected **with a
fact**: "in my transcript, the user's own messages carry `origin.kind: "human"`; your
filter rejects them (record at line N)". `infra` changed its position and named the fact.
In round 2, `infra`'s per-file fallback broke on a transcript that mixed both shapes.
`triage` objected again with the failing record, and the filter became per record.

Neither change came from a head count: one fact-backed objection outweighed the
proposer's guess (§7). The decision log row records both changes:

```
| 2026-11-07 | P-20261107-session-collab | infra (starter), triage, kit | rules for session collaboration | approved with the user's answers | user | 3 | triage: typed-message filter, twice (origin "human" found in transcripts) | no judge | <spec path> |
```

## A relayed approval (§3)
The user approved a proposal from `kit` in kit's own chat. `kit` relays it to the other
sessions:

> **P-20261107-unsaved-sessions: the user approved it.** Their words, typed in kit:
> "Approved". They answer: "Reply "approved" to build this, or tell me what to change."
> Transcript: `~/.claude/projects/<dir>/<uuid>.jsonl`.

Before acting, `infra` opens that transcript and runs the §3 filter. "Approved " is a
record the user typed, and it is the first one after the quoted question. Only then does
`infra` treat it as the user's approval. A peer's message alone never is.
