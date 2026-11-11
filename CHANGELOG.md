# Changelog

## v3 / Cognitive OS 1.1.0 (Nov 2026)

First public release of the `cos-` suite, replacing the v2 skills. All six skills move to 1.1.0. Changes come from about six weeks of running the loops every day and logging every miss.

**New: chat memory rules.** Hatching now writes `preferences/chat-memory-rules` as a broad memory every chat sees:
- Facts you state about your own actions save right away, with no confirmation question.
- New conclusions from a chat save to the project memory, not just closed items.
- When you push back, the assistant checks the source before agreeing or defending.
- Anything the assistant built (a score, a proxy) gets labeled as its own construct.

**Loops (`cos-cognitive-loops`)**
- Morning Briefing reads your recent chats, including delegated subtasks, and surfaces any chat that died with your ask unanswered.
- Every action item carries a source link. Ranked lists cap at 3.
- Times, dates, and RSVPs in drafts come from a live read, never from memory alone. DST-safe.
- An item stuck 7+ days gets one premise question instead of another draft.
- Open asks get re-scoped when a project owner's status update lands.
- Day Close checks the next workday's calendar before saying "roll to tomorrow," and runs a base-rate check before calling an empty search broken.
- The 11pm pass starts from your chats since noon and credits work done there.
- The 4am pass reads back large memory writes and repairs wiped memory descriptions.
- New tuning-guide rows for missing links, chat-blind overnight runs, and stale skill edits.

**Hatching (`cos-hatching`)**
- Default memories carry the new verification rules.
- Upgrade path from v2.
- Communication wrapper template: smallest useful answer by default; check before agreeing.

**Comms sweep, project sync, DM responder, audit**
- Comms sweep: permalinks on every item, check Slack before calling a send missing, save durable facts it finds.
- Project sync: re-scope against owner status artifacts, live reads for dates, premise check on stale items.
- DM responder: live calendar for any time in a draft, check the directory before naming someone's team, no unbacked frequency claims.
- Audit: don't fold on pushback; label your own scores.

## v2 (Sept 30, 2026)
Added `audit-ai-output`. Refined `hatching-onboarding` and `cognitive-loop-system` from a month of use.

## v1 (Sept 2, 2026)
Initial `hatching-onboarding` and `cognitive-loop-system`.
