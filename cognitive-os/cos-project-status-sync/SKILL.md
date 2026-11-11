---
name: cos-project-status-sync
description: "Use when the user says 'sync my projects', 'update the tracker', 'where do things stand?', or after a heavy week. Reconciles project memories and the user's tracker with actual activity across Slack, email, Drive, calendar, and past chats, then proposes updates."
metadata:
  compatible-agents: genie
  version: "1.1.0"
  suite: cognitive-os
---

# Cognitive OS: Project Status Sync

Closes the gap between where work happens (chats, meetings, email, Slack, docs) and where it's tracked (the `projects/*` memories and the user's own tracker, if any). The loops catch things in real time and the 4am pass audits memory. This skill does a full reconciliation per project and pushes updates back.

## When to Activate

- "Sync my projects," "update the tracker," "where do things stand?"
- A loop flags that a project's tracked status looks stale
- After a heavy meeting week
- Weekly as a scheduled task (suggest Friday afternoon)

## Two Tracking Surfaces

1. **`projects/*` memories:** what the assistant and the loops read. Updated directly.
2. **The user's tracker** (from `reference/project-tracker`): a Google Doc, sheet, Jira board, Notion page, or "none." If it's "none," the memories are the only surface and you skip the tracker steps.

When the two disagree, flag it. Don't silently overwrite either one.

## Per-Project Sources

Each `projects/<name>` memory should carry a short **Sources** line: Slack channels, key collaborators, email keywords, Drive folder, Jira project. If a project has no Sources line, build one from its collaborators and name, show it to the user, and save it to that project memory after they confirm.

## Workflow

### Phase 1: Read current state
1. Read `profile` for the active project list.
2. `memory_view` every `projects/*` path. Note "as of" dates, open items, status.
3. Read the tracker if there is one. Note differences from memory.
4. For each project, the last recorded update is the start of the search window.

### Phase 2: Fan out (batch by source, not by project)
- **Slack:** project channels and DMs with collaborators. Look for decisions, blockers, assignments, scheduled or canceled meetings.
- **Email:** threads with collaborators and project keywords. Approvals, escalations, follow-ups, shared docs.
- **Drive:** recently changed or newly shared docs tied to the project.
- **Calendar:** meetings since the last sync with collaborators (likely produced updates), cancellations (possible deprioritization), new recurring meetings (new workstream).
- **Meeting notes:** per `reference/notes-source`.
- **Past chats:** `episode_search` for the project. Decisions and corrections the user made in chat are authoritative.
- **Jira/Confluence** if connected.

### Phase 3: Reconcile
For each project: new information, status changes, new blockers, people changes, timeline shifts, decisions, contradictions between tracker and reality.

Before recording any task or deadline as the user's, name the owner from the source. Unassigned or "the group" is FYI.

### Phase 4: Update (tiers per `preferences/self-improvement-approach`)
- **Auto-apply:** factual fixes, confirmed completions, new links, unambiguous status updates
- **Propose-apply:** new open items or blockers, status summary rewrites, new collaborators
- **Propose-wait:** priority changes, marking dormant or done, merging or splitting projects, adding a new project

### Phase 5: Report
**In chat:** lead with the most important change across all projects. Only show projects that moved. Per project: what changed, what's new, what needs the user. End with proposals.

**As a scheduled task:** follow `preferences/scheduled-task-output-style`. One line per update. Proposals last.

**Tracker updates:** if you can write to the tracker, propose the exact edits and apply after OK. If not, show a diff: "Under [Project], change [old] to [new]."

## Principles

1. Every status change cites a message, email, doc, or meeting. No inferring from silence.
2. Silence is worth noting, not interpreting. Flag it.
3. The user can absorb 5 to 10 updates at a time. If 8 projects moved, prioritize.
4. Don't re-report what a loop already caught today.
5. When a milestone lands, list what it did NOT resolve.
6. When the project owner's status artifact (weekly update, plan sheet, MBR deck) is newer than memory, re-scope open asks against it. It can shrink, re-own, or add scope. "Still owed" needs evidence too.
7. Dates, times, and RSVPs come from a live read, not from the memory you're reconciling.
8. If an open item has sat 7+ days with no movement, ask the user whether it's still theirs before carrying it forward.
9. Save new conclusions from the sync (not just closes) to the project memory.
