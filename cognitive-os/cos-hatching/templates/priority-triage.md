# Template: Priority Triage (build per user)

Build this in week 2, once 3+ projects are tracked. Fill every [PLACEHOLDER] from `profile`, `reference/project-tracker`, `reference/connected-sources`, and `preferences/calendar-response-status`. Save as the user's personal skill `priority-triage`. Never ship the filled version.

---

```
---
name: priority-triage
description: "On-demand 'what should I focus on next?' for [USER]. Cross-references the tracker, calendar, email, and Slack, verifies what's actually done, then ranks what's left."
---

# Priority Triage

## When to use
"What should I focus on?", "what's on my plate?", "triage my priorities", or [USER] says they finished something and wants what's next.

## Phase 1: Gather (in parallel)
1. Tracker: [TRACKER TYPE + ID from reference/project-tracker, or "projects/* memories" if none]
2. `profile`: who sets priorities ([PRIORITY SETTER]) and open asks
3. Calendar: today + next 2 days, tiered per preferences/calendar-response-status
4. Email: unread, last 3 days, action-requested only
5. Slack (if connected): mentions of [SLACK USER ID from reference/comms-channels], last 3 days
6. [ANY EXTRA SOURCE THE USER NAMED, e.g. a shared drop folder]

## Phase 2: Verify
For each open item, look for evidence it moved: a sent message, a doc edit, a sent email, a closed ticket.
Mark: Done (with evidence), In Progress, Not Started, Blocked (waiting on someone).
When [USER] says something is done, find the receipt.

## Phase 3: Rank
1. Due today or overdue
2. Blocking someone else
3. Priorities set by [PRIORITY SETTER]
4. Follow-ups with deadlines
5. Side projects with momentum
6. Research or low urgency (mention, don't dwell)
Due dates beat tiers.

## Output
Ranked list: item, status with evidence, next action, who's involved, when.
End with a top-5 focus list, one line each.

## Rules
- Only items directed at [USER] and stated as their priority. Attending a meeting isn't owning its deliverables.
- No methodology. Just the list.
```
