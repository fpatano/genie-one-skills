---
name: cos-comms-sweep
description: "Use when the user asks 'what did I miss?', 'sweep my comms', 'check my inbox', or is catching up after meetings or a weekend. Sweeps Gmail and Slack, sorts everything into Direct, Project, and Ambient tiers, and offers drill-downs or drafts."
metadata:
  compatible-agents: genie
  version: "1.1.0"
  suite: cognitive-os
---

# Cognitive OS: Comms Sweep

An on-demand catch-up. Sweeps the user's Gmail and Slack, sorts what it finds into three tiers, and offers to go deeper or draft replies. It complements the scheduled loops; it doesn't replace them.

## When to Load

"Sweep my comms," "check my inbox," "what did I miss?", "process my inbox," or right after a meeting block, a heads-down stretch, or a weekend.

## Step 0: Load Context

Read these memories. All come from `cos-hatching`.

1. `profile`: active projects and key collaborators
2. `projects/*`: project names and people, for the keyword list
3. `reference/comms-channels`: the user's Slack user ID, channel groups (Core, Product/community, Only-if-mentioned), and keyword list
4. `reference/notes-source`: where meeting notes arrive, if anywhere

**If `reference/comms-channels` is missing:** run Gmail only, then ask: "Want me to add Slack? Tell me the 5 to 15 channels that matter and I'll remember them." Look up the user's Slack ID with the Slack user search tool and save everything to `reference/comms-channels`.

Build the keyword list from project names, collaborator names, and any product terms stored in `reference/comms-channels`.

**Time window.** Default is today. Accept "last 2 hours," "since yesterday," "last 3 days." For Slack multi-day windows, run one `on:YYYY-MM-DD` search per day.

## Signal Tiers

| Tier | Qualifies | Treatment |
|------|-----------|-----------|
| 🔴 Direct | @-mentions, DMs, emails addressed to the user by name with real content, replies to the user's messages | First. Summary plus recommended action. Draft-ready. |
| 🟡 Project | Activity touching an active project, even without a tag | Second. Grouped by project. |
| ⚪ Ambient | Everything else in monitored channels worth a glance | Last. One line each. |

## Sweep (run phases in parallel; aim for 6 to 8 Slack calls total)

### Phase A: Slack mentions (one call)

```
slack_search_public_and_private(
  keywords=["<@SLACK_USER_ID>"],
  filters="on:YYYY-MM-DD",
  sort="timestamp", sort_dir="desc"
)
```

`SLACK_USER_ID` comes from `reference/comms-channels`. This catches mentions everywhere, including DMs and group DMs.

### Phase B: Channel sweep

For each **Core** and **Product/community** channel in `reference/comms-channels`:

```
slack_search_public_and_private(
  filters="on:YYYY-MM-DD in:#channel-name",
  sort="timestamp", sort_dir="desc", limit=10,
  natural_language_query="[project keywords]"
)
```

Batch 4 or 5 channels per round.

### Phase C: Only-if-mentioned channels

Don't sweep these. Read one only if Phase A found a mention in it.

### Phase D: Gmail

```
gmail_search(query="is:unread in:inbox newer_than:1d")
```

Adjust the window to match the request.

**🔴 Direct (email):** user is in To, addressed by name, real content. Calendar invites from people that need a reply.

**🟡 Project (email):** subject or snippet matches a keyword. Meeting notes (per `reference/notes-source`) for a project meeting.

**Noise, never surface:**
- The user's own scheduled-task outputs (Genie scheduled task emails)
- Bounces and mailer-daemon
- Tool notifications (task trackers, CI, ticketing bots) unless they name the user
- Marketing, newsletters, promo
- Signature-only replies, empty calendar accepts

## Classification Notes

- A keyword match lifts an item to 🟡 unless it also mentions the user, then it stays 🔴.
- An email in To with only a signature or an empty accept is noise.
- Filter out Slack bot and workflow messages (search assistants, security bots, on-call bots).

## Output

Lead with 🔴, then 🟡, then ⚪. First 2 or 3 lines are the most important. No "Here's your sweep."

```
🔴 **[Slack/Email]** [Person]: [summary]. → [recommended action]

🟡 **[Project]**
- [channel/email]: [development]

⚪
- **#channel**: [one line]

_Filtered: X bounces, Y notifications, Z scheduled-task emails._

Want me to go deeper on any of these, or draft a reply?
```

Skip any empty tier. If nothing needs attention, say so in one line and stop.

## Drill-Down

- "Tell me more about #3": read the full thread or email
- "Draft a reply to [person]": load the user's `communication-wrapper` skill if it exists; for Slack DMs, use `cos-slack-dm-responder`
- "What else is in #channel": read and summarize that channel
- "Expand [project]": everything on that project across Slack and email

## Hard Rules

1. Never auto-send. Draft first, always, unless the user explicitly changes that.
2. Don't surface the user's own scheduled-task emails.
3. Don't repeat what today's morning briefing already covered.
4. Only flag action needed if it's directed at the user. Don't inflate their list.
5. Only-if-mentioned channels are read only on a mention.
6. Silence is a valid result.
7. Every item carries its permalink so the user can jump straight to it.
8. Checking whether the user already replied? Look in Slack first. Many people send from Slack, so an empty Gmail Sent over one day is often just true. Widen the window before calling a check broken.
9. If the sweep turns up a durable fact (a reorg, a commitment, a decision, a date change), save it to the right project memory in the same turn.

## Slack Search Notes

Works: `on:YYYY-MM-DD`, `in:#channel`, `<@USERID>` as a keyword, sorting by timestamp, combining `on:` and `in:`.
Unreliable: `after:YYYY-MM-DD`, the `oldest` parameter on channel reads, searching the user's first name (too broad).

## Maintaining the Channel List

When the user says "add #x" or "stop watching #y," update `reference/comms-channels` in the same turn. If the 4am self-improvement pass sees the user regularly asking about a channel that isn't listed, it can propose adding it.
