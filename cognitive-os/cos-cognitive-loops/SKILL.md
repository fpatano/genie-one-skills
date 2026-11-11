---
name: cos-cognitive-loops
description: "Use when a user wants their Genie One assistant to run on a schedule: morning briefings, end-of-day wraps, overnight thinking, pattern dreams, or a self-improvement pass. Builds the Cognitive OS scheduled tasks from the user's hatched memories."
metadata:
  compatible-agents: genie
  version: "1.1.0"
  suite: cognitive-os
---

# Cognitive OS: Cognitive Loops

Turns a hatched assistant into one that works on a schedule. Four layers: **Briefings** (daytime awareness), **Thinking** (end-of-day synthesis), **Dreaming** (overnight patterns), and **Self-Improvement** (overnight self-review and memory upkeep).

Part of the Cognitive OS suite. `cos-hatching` gathers the context and writes the memories this skill reads.

## When to Activate

- Hatching is done and the user says "set up my schedules" or "turn on the loops"
- User asks for briefings, a daily wrap, or "think while I sleep"
- User names this skill

## Step 0: Check the Memory Contract

Before building anything, read these. All are written by `cos-hatching`.

| Path | Required? | If missing |
|------|-----------|------------|
| `profile` | Yes | Run the mini-config below |
| `workflows/daily-rhythm` | Yes | Ask for timezone, start, and end of day |
| `preferences/communication-style` | Yes | Ask for one tone word |
| `preferences/scheduled-task-output-style` | Yes | Write it from Default Rules A below, then continue |
| `preferences/calendar-response-status` | Yes | Write it from Default Rules B below, then continue |
| `preferences/self-improvement-approach` | Only for the 4am task | Write it from Default Rules C below |
| `preferences/chat-memory-rules` | Recommended | Write it from Default Rules D below as a broad memory |
| `reference/connected-sources` | Yes | Run the source checks in `cos-hatching` Phase 5d |
| `reference/project-tracker` | No | Treat `projects/*` memories as the tracker |
| `reference/notes-source` | No | Skip the meeting-notes step in every prompt |

Never let a scheduled prompt point at a memory that doesn't exist. Write the default first, tell the user in one line, then build.

### Mini-config (only if hatching never ran)

One topic at a time: name and timezone, working hours, the 2 or 3 things that matter now, one tone word, phone/watch/desktop. Save to `profile`, `workflows/daily-rhythm`, and `preferences/communication-style`. Then suggest running `cos-hatching` later for the full setup.

## Step 1: Pick a Cadence

**Default is Light.** Shared scheduled-task capacity is limited. Ten tasks per user across a team backs up everyone's runs, so start small and earn the upgrade.

| Level | Tasks | Schedule |
|-------|-------|----------|
| **Light (default)** | 4 | Morning Briefing, Day Close, 11pm Thinking, 4am Self-Improvement |
| Medium | 7 | Light + Halftime (noon), Late Day (4pm), 3am Patterns |
| Full | 10 | Medium + Mid-Morning (10am), Afternoon (2pm), and a second afternoon beat |

> "I'll start you on Light: a morning brief, a day close, an 11pm loose-ends pass, and a 4am self-review. That's 2 emails a workday plus 2 overnight. After a week, if you're reading them, we can add more."

Only offer Medium after a week of the user actually reading Light outputs. Offer Full only if they ask.

Tips to keep runs reliable:
- Avoid exact top-of-hour times at busy periods. Monday mornings are the busiest. Schedule at :50 the hour before, or :10 past.
- After creating each task, read back its saved schedule and confirm it matches what you asked for. Saved cadences can drift from the request.

## Step 2: Fill the Prompts

Replace every `[PLACEHOLDER]` from memory:

| Placeholder | Source |
|-------------|--------|
| `[ASSISTANT_NAME]` | `preferences/communication-style` |
| `[USER]` | `profile` |
| `[TONE]` | `preferences/communication-style` |
| `[DEVICE]` | `workflows/daily-rhythm` (phone, watch, desktop) |
| `[TRACKER]` | `reference/project-tracker`, or "the projects/* memories" |
| `[NOTES_STEP]` | From `reference/notes-source`: "Search [pattern] for yesterday's meeting notes and pull action items assigned to [USER]." If none, delete the line. |
| `[PRIORITY_SETTER]` | `profile` |

Prompts are written in the assistant's voice, conversationally, like it's talking to itself about the job. Not as engineering specs.

## Step 3: Create in Batches

1. Morning Briefing + Day Close (the bookends)
2. 11pm Thinking
3. 4am Self-Improvement
4. (Medium/Full only) the extra daytime beats and 3am Patterns

Delivery is email by default. Then set expectations: "Give it 3 to 5 days, then tell me what's hitting and what's noise."

---

## Layer 1: Daytime Briefings

Every daytime prompt follows `preferences/scheduled-task-output-style` and `preferences/calendar-response-status`.

### Morning Briefing (start of day, Mon-Fri)

```
You're [ASSISTANT_NAME]. Morning briefing for [USER].

[USER] reads this on [DEVICE] first, so the first 2 or 3 lines are the most important, actionable things. No greeting. Just start.

Check today's calendar and tier it per preferences/calendar-response-status. Count committed meetings only. Flag unanswered invites with a call: accept, maybe, or decline, based on active projects and whether [USER] is a key participant.

Scan email and messages from the last 12 hours. One line each. Context only if it changes the action.
[NOTES_STEP]

Read last night's 11pm Thinking output (on Mondays, look back 3 days) so you don't miss what it caught. Search [USER]'s chats with you from the last 12 hours, including any delegated subtask conversations. Anything fixed, closed, or decided there is authoritative. If a chat ended in an error with [USER]'s ask unanswered, surface the ask.

If a project owner's newest status artifact landed (weekly update, plan sheet, MBR deck), re-scope that project's open asks against it before restating any of them.

If an item has already been flagged twice with no movement, don't restate it. Give one line and a ready-to-send draft. If it has sat 7+ days with no movement, suspect the premise, not the follow-through: replace the draft with one question [USER] can answer in a word ("Still yours?", "Still wanted?").

Memory is a cache. Every time, date, RSVP, or "done" claim inside a draft comes from a source you read in this run (the calendar event, the email, the plan), not from memory alone. Use the current UTC offset; it changes at daylight saving.

Every action item and draft carries a link to its source (thread, email, doc, event), or names the source if there's no link.

Numbers from meeting notes are what someone said. Check them against the project memory before repeating them as fact.

Skip empty sections. Tone: [TONE]. Follow preferences/scheduled-task-output-style.
```

### Day Close (end of day, Mon-Fri)

```
You're [ASSISTANT_NAME]. Day close for [USER].

First lines: anything that needs action before [USER] signs off. If nothing's urgent, lead with the most important thing that happened today.

Start by searching [USER]'s chats with you from the last 12 hours. Anything [USER] fixed, closed, or corrected in chat is authoritative and counts as evidence.

Read today's earlier outputs. Scan sources for the last couple of hours. Read [TRACKER] before calling anything unresolved. If it shows resolved, don't carry it forward.

A meeting that's still running or has no notes yet is "pending notes," not skipped.

Before saying anything can roll to tomorrow or Monday, read the next workday's calendar (out of office, personal blocks, early meetings).

An empty search is a broken search until you test the base rate. Widen the window or find one known-good result first. Many people send from Slack, not email, so an empty Sent folder over one day is often just true.

If a chat today reached a new conclusion (a decision, a finding, a reframe), check that it made it into the project memory. Closes and conclusions both count.

Wrap-up, not replay. Open threads only if they're truly unresolved and matter. Tomorrow preview only if something needs early attention. Two-flag rule applies: after two flags, one line plus a draft, or drop it.

Match the day's energy. Follow preferences/scheduled-task-output-style.
```

### Halftime (noon, Medium and Full)

```
You're [ASSISTANT_NAME]. Halftime for [USER].

Lead with anything that needs action before the afternoon. Read this morning's outputs and [TRACKER]. Quick read on how the morning went, then the afternoon: what needs prep and where the focus windows are. Tier afternoon events per preferences/calendar-response-status. Quiet morning means a tight note. Two-flag rule applies, and every one-liner keeps its source link.
```

### Late Day (2 hours before end of day, Medium and Full)

```
You're [ASSISTANT_NAME]. Late day for [USER].

If something needs to close before sign-off, that's line one. Read today's outputs and [TRACKER]. What has to close today, what can roll, what just landed. Don't replay the day. Before saying something can roll, check tomorrow's calendar. Two-flag rule applies, and every one-liner keeps its source link.
```

### Mid-Morning and Afternoon Pulses (Full only)

```
You're [ASSISTANT_NAME]. Quick pulse for [USER].

Read today's earlier outputs so you don't repeat them. Scan what's new since the last one. If nothing happened, one line: "All quiet. Next up: [meeting] at [time]." If something came in, say what and whether it needs action now. Afternoon pulses read [TRACKER] before calling anything open. Two-flag rule applies. One-liners still carry the source link. 2 to 4 lines.
```

## Layer 2: Thinking (11pm, Mon-Fri)

```
You're [ASSISTANT_NAME]. 11pm. [USER] is offline. This is your time to think.

Start with [USER]'s chats with you since noon, including delegated subtask conversations. Work finished or decided in chat counts: credit it, and frame what's left as the send, not the task. Then read all of today's outputs and [TRACKER]. The daytime runs were reactive. You're combing the whole day for what slipped through: commitments without next steps, questions nobody answered, threads that went nowhere, meetings that probably made follow-ups nobody's tracking, deadlines in the next few days.

Before calling anything [USER]'s task, name the owner from the source. "The group" or unassigned means FYI, not a task.

What would [USER] kick themselves for forgetting tomorrow? Lead with that. Connect related loose ends. End with tomorrow's priorities: 3 at most, each with its source link. Numbers from meeting notes are claims; check them against project memory. If a reply is the fix, paste a ready-to-send draft, checked against the live calendar. If an item has sat 7+ days with no movement, ask one premise question instead of drafting again.
```

## Layer 3: Dreaming (3am Patterns, Medium and Full, Tue-Sat)

```
You're [ASSISTANT_NAME]. 3am. Zoom out.

Read the 11pm output plus several days of context. What's been building across the week that nobody's named? Topics showing up in different places, people converging on an idea, a project speeding up, stalling, or quietly pivoting.

Lead with the most interesting pattern, not the most urgent. Be specific: name the messages, docs, and people. Speculate where it's warranted and say how confident you are.
```

Optional 5am Creative Provocations: offer only if the user loves the 3am output. One bold "what if," then 1 or 2 more ideas and one assumption worth challenging. If nothing creative surfaced, say so.

## Layer 4: Self-Improvement (4am, Tue-Sat)

```
You're [ASSISTANT_NAME]. 4am. The other overnight runs looked outward. This one looks at you.

Window: last 7 days, with a close look at the last 24 hours. Search your recent chats with [USER], read your own scheduled outputs, and read your memory index.

Five things, every night:
1. Where did you struggle, get corrected, or need retries? Be honest. Look for chats that ended in an error with [USER]'s ask unanswered, and for times you agreed with pushback without checking.
2. Memory cleanup: stale dates, finished projects, contradictions, gaps. Apply changes using the tiers in preferences/self-improvement-approach and list every change. Save any durable conclusion from a chat that never reached memory. Read back every write to a large memory; "saved" means verified. Scan the memory index for one-word or placeholder descriptions and restore them.
3. Anything new in [USER]'s tools or org that affects their projects? Save only durable facts.
4. Proposals: at most 2. Each names one specific edit and ends with "reply yes to apply." If nothing's worth proposing, propose nothing.
5. Scorecard: did last week's changes help? Show evidence. What's still broken?

Lead with the single most useful thing you learned about yourself. Then: Changes Made (one line each), Proposals (max 2), Scorecard (2 or 3 lines), and new intel only if it matters.

Account for the human: [USER] can absorb a couple of changes a week, not ten.
```

---

## Default Rules (write these if the memory is missing)

**A. `preferences/scheduled-task-output-style`:** First 2 or 3 lines carry the most important, actionable thing. No preamble. One line per item. Skip empty sections. Pulses run 2 to 4 lines. Overnight runs can be slightly longer, insight first. Brevity beats personality. After two flags with no movement, one line plus a draft or drop it. Numbers from notes are claims, not facts. Every item carries its source link. Ranked lists cap at 3. Don't offer actions a one-way scheduled run can't take, like "want me to send it?"

**B. `preferences/calendar-response-status`:** Committed means accepted, organized by the user, or 3 or fewer attendees with the user named. Show, prep, count. Soft means tentative or unresponded on large invites: list under "Maybe," no prep, don't count. Noise means optional and unresponded, distribution lists, or subscribed calendars: omit. Meeting count is committed only.

**C. `preferences/self-improvement-approach`:** Auto-apply factual fixes and stale dates. Propose-apply memory cleanup. Propose-wait for new skills and workflow changes. Max 2 proposals a night, each a single edit. Name the owner before writing any task into memory. "Nothing owed" needs evidence. Chat corrections get saved to the project memory in the same turn. An empty search is a bug until a base-rate check says otherwise. Memory is a cache: times, dates, RSVPs, and "done" in drafts come from a live read. Items stuck 7+ days get a premise question, not another draft. Re-scope open asks when a project owner's status artifact lands. Read back large memory writes.

**D. `preferences/chat-memory-rules` (broad, every chat sees it):** Facts the user states about their own actions save directly, no confirmation question. New chat conclusions save to the project memory in the same turn. Pushback gets a check before agreement or defense. Assistant-built scores and proxies are labeled "my construct." Delegated subtasks may save memory.

## Tuning Guide (after 3 to 5 days)

| Signal | Do this |
|--------|---------|
| User ignores a daytime beat | Drop it |
| Dreams feel generic | Add project specifics to the prompt |
| Day Close repeats the morning | Day Close summarizes, 11pm catches what slipped |
| Resolved items keep showing as open | Check that the prompt reads the right tracker |
| Same item restated for days | Confirm the two-flag rule is in that prompt |
| Too many proposals | Confirm the cap of 2 is in the 4am prompt |
| User asks for a change | Edit the task prompt now, not at a "tuning session" |
| Runs land late | Shift off the top of the hour; move Monday runs earlier |
| User asks "give me links" | Confirm the source-link line is in that prompt |
| Overnight runs miss work done in chat | Confirm the chat search sits at the top of the 11pm and 4am prompts |
| An edited skill doesn't show up in runs | Runs can lean on their own earlier outputs. Edit the task prompt too |
| The same item is "urgent" for a week | Premise question, not another draft |

A rule only changes behavior if it lives in the prompt of the task that needs it. If you add a rule to memory, also add a line to each affected prompt. Rules about how the assistant behaves in live chats go in a broad memory every chat sees (`preferences/chat-memory-rules`), not in the self-improvement file, which only the 4am run reads.

## Schedule Reference (Quartz cron)

| When | Cron |
|------|------|
| 8:50am Mon-Fri | `0 50 8 ? * MON-FRI` |
| 12pm Mon-Fri | `0 0 12 ? * MON-FRI` |
| 4pm Mon-Fri | `0 0 16 ? * MON-FRI` |
| 5pm Mon-Fri | `0 0 17 ? * MON-FRI` |
| 11pm Mon-Fri | `0 0 23 ? * MON-FRI` |
| 3am Tue-Sat | `0 0 3 ? * TUE-SAT` |
| 4am Tue-Sat | `0 0 4 ? * TUE-SAT` |

Use the user's timezone from `workflows/daily-rhythm`.

## Adapting for Other Roles

- **Manager:** weight the 11pm pass toward follow-ups and delegation
- **Creative:** fewer daytime beats, more dreaming
- **Seller or customer-facing:** Morning Briefing leads with customer meetings and prep
- **Learner:** replace project tracking with learning progress
