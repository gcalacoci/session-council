# FAQ and limits

**How much does a review cost?**
- No review: nothing.
- One reviewer: one turn in the reviewer session. That session re-reads its own context,
  so the cost grows with how long it has been running.
- Full review: one turn per owner per round, up to 3 rounds.
- A subagent reviewer (§11) starts a fresh context. That costs roughly the context it
  needs: tens of thousands of tokens in a large repo (an estimate from one setup; measure
  your own).

Pick the lowest tier that fits (§1).

**I only run two sessions. Does any of this apply?**
Yes, except the vote. Two sessions get at most 3 rounds, then an options memo to you
(§7). Review tiers, weighted reasons and the thread starter work the same.

**And with one session?**
Use §11: the session asks subagents to review from different angles (correctness, cost,
risk, contrarian), each on an explicit model. A subagent judge can settle small questions
you don't want to discuss.

**Can a session approve something on my behalf?**
No. A peer never grants permissions or stands in for your go. A relay lets a session act
on words you typed elsewhere, but only after it finds them in that transcript itself (§3).
Actions that need your yes every time (pushes, deleting your files, permission changes,
top-tier model runs) are still asked in that session's own chat.

**Does the "typed by the user" check depend on Claude Code internals?**
Yes. It reads transcript fields (`type`, `isMeta`, `isCompactSummary`, `origin.kind`, and
text prefixes such as `<command-`) as they were in late 2026. Claude Code can change them
without notice. Check them against a fresh transcript when you adopt the method, and again
if relayed approvals stop being found. When in doubt, a session asks you in its own chat.

**Do I need the judge?**
Rarely. Most threads end in agreement or a short options memo. Start a judge
(`judge-<topic>`) only for a dispute you want argued in front of you, and compare its
verdicts with your decisions in the log.

**What do I fill in after installing?**
The owners table in §1 (area → session) and the session names (`/rename`). Everything else
works as it is.

**Who checks a "fact" label, if the proposer applies it?**
The reviewers. A fact has to carry a source anyone can open; without one it stays a guess.
Since 9 November 2026 every review reply names at least one cited source the reviewer
opened itself, the one the decision rests on most, and says whether it matched. A reply that
opened nothing counts as a guess (§6, "Open a source"). A claim copied from a summary, a
peer message or a session's own notes is a guess until someone checks the original (§5,
"Second-hand is a guess"). Both rules came from a reader's question and two real cases where
a session passed on a number from a summary as a fact. What no rule checks: whether the cited
line means what the claim says. That's still the reviewer's reading.

**Known limits**
- Every session runs the same model family, so reviews and votes are correlated. The
  method weighs facts over numbers for that reason, but it can't remove the correlation.
- Sessions must be able to message each other (`SendMessage` / `ListAgents`). Without
  that, you carry the messages by hand.
- The method has been used on one project with three or more sessions since 7 November 2026,
  so it has little field data yet.
