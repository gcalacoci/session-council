# Why it works this way

The method came out of a 3-round review among three sessions, which the user then
approved. These are the main choices and what each one guards against.

## One chat for the user (§3)
With several sessions, every one of them asks for a "go", and the user ends up jumping
between chats. The thread starter collects the questions, and approvals reach other
sessions as relays. A relay carries the user's exact words and the transcript where they
were typed, so the receiving session can check them itself instead of trusting the peer.

## Reasons with weights (§5)
Sessions running the same model agree easily. An agreement backed by "I checked" is
worthless if nobody can see what was checked. Labelling each claim as fact, guess or no
basis makes a review something the user can audit. It also gives "a fact beats a head
count" something to stand on.

## Blind first round, no performative agreement (§6)
A reviewer that reads the others' replies first tends to anchor on them. Polite agreement
("great point", "you're right") passes for review without being one. Both failure modes
are documented for LLM debate: debate without a judge collapses into early agreement
(arXiv 2509.23055), and debate can flip correct answers to wrong ones ("Talk Isn't Always
Cheap").

## A fact beats a head count (§7)
All sessions run Claude models, so their votes are correlated: three sessions agreeing on
a guess is closer to one opinion than three. A single objection backed by a checked fact
therefore outweighs a majority resting on guesses, and the user gets an options memo
instead of a tally. The starter can't overrule an objection against its own side.

## The vote recommends, the user decides (§7)
No vote or judge can approve anything on the user's behalf, or override the hard rules
(pushes, deleting, personal data, permissions, paid escalations). That leaves the method
safe to run with long-lived sessions that act on their own.

## The judge (skill `session-judge`)
LLM judges show position, length and own-family bias (Zheng et al., arXiv 2306.05685).
The judge writes its scoring rubric before reading the briefs, reads them in both orders,
ignores length, and notes its model family. Its verdict goes into the decision log next to
the user's decision. After about 5 cases you can see whether it is worth trusting more.

## Helper models stay out of decisions (§9)
Cheap or free models are good at bulk code and reading. They don't get a vote, a review or
a judgement, because their mistakes would arrive with the same confidence as a fact.
Tests, linters and scripts are different: their output is a fact.

## Review scales with the work (§1)
A full council for a one-line fix costs more tokens than the fix risks. Most work needs no
review or a single reviewer. The full review is for live jobs, shared files and anything
hard to undo.

## Prior art
Searched November 2026. Nothing found had a thread starter, weighted reasons or a vote
across separate sessions; the closest work runs inside one session. Ideas borrowed:
- **ECC `santa-method`** (affaan-m/ECC, MIT): two independent reviewers, at most 3
  iterations, then a human; fresh reviewers each round.
- **ECC `council`, `council-multi-model`**: each voice writes its position before reading
  the others; same-model review is marked as weaker.
- **obra/superpowers `receiving-code-review`** (MIT): no "You're absolutely right!"; check
  against the code before agreeing.
- **warpdotdev/common-skills `council`** (MIT): model diversity first, a distinct angle per
  member (used in §11).
- **neolabhq `judge-with-debate`** (GPL-3.0; idea only, no text copied): the rubric comes
  before any judge reads the case.
- Claude Code docs (agent teams, cross-session messaging): a peer message can't approve
  anything; §3's transcript check sits on top of that.
