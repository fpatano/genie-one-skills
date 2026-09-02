---
name: cognitive-loop-system
description: "Blueprint for implementing a three-layer cognitive loop system (Briefings, Thinking, Dreaming) as scheduled tasks on any hatched personal assistant Genie instance."
metadata:
  compatible-agents: genie
---

# Cognitive Loop System

A complete architecture for turning a hatched personal assistant into an always-on cognitive system. Three layers run on a schedule: **Briefings** (daytime awareness), **Thinking** (end-of-day synthesis), and **Dreaming** (overnight creative processing).

This skill works in tandem with the **hatching-onboarding** skill. Hatching gathers the identity, priorities, style, and rhythm that this skill uses to configure the loops. If hatching is already complete, this skill picks up that context and builds the schedules. If not, it runs its own lightweight configuration conversation to gather what it needs.

---

## When to Activate

- After hatching is complete and the user says "set up my schedules" or "turn on the cognitive loop"
- User asks for briefings, thinking, or dreaming schedules
- User references this skill by name
- The hatching skill's Phase 5 (Daily Rhythm) is complete and the user wants to go live
- User says "I want my assistant to think while I sleep" or similar

---

## Relationship to Hatching

### If Hatching Is Complete (Preferred Path)

The hatching process already gathered:
- ✅ Identity (name, role, timezone) → stored in `profile`
- ✅ Priorities and projects → stored in `projects/`
- ✅ Communication style → stored in `preferences/communication-style`
- ✅ Daily rhythm preferences → stored in `workflows/daily-rhythm`
- ✅ Connected sources → stored in `reference/connected-sources`

**In this case:** Skip the configuration conversation. Pull context from memory and go straight to implementation. Confirm the schedule with the user, then create the tasks.

### If Hatching Is NOT Complete (Standalone Path)

Run the Configuration Conversation below to gather the minimum context needed. This is a compressed version of hatching focused only on what the cognitive loop requires.

---

## Configuration Conversation (Standalone Path)

When the cognitive loop is being set up without a prior hatch, gather these essentials conversationally. Same rules as hatching: one topic at a time, match their energy, don't interrogate.

### Step 1: Identity Basics

> "To set up your cognitive loop, I need a few things. First — what should I call you, and what's your timezone?"

Need: Name/address, timezone, working hours (start and end of day).

### Step 2: What Matters

> "What are the 2-3 things you're focused on right now? Projects, goals, areas of your life — whatever takes up your mental bandwidth."

Need: Enough context to make the dream states useful. Without knowing what the user cares about, pattern recognition and creative provocation are generic and useless.

### Step 3: Sources Check

> "Let me check what I can see..."

Run source verification (same as hatching Phase 5b):
- Attempt Calendar read
- Attempt Email read
- Attempt Drive read
- Attempt any messaging platform read

Report what's connected and what's missing. Don't block on missing sources — work with what's available.

### Step 4: Cadence Preferences

> "Here's the default setup: hourly pulse checks during your workday, a deep-thinking pass at 11pm, and two creative dream states overnight (3am and 5am) so you wake up with fresh perspectives. That's 12 scheduled tasks total. Want the full spread, or should we start lighter?"

Options to offer:
- **Full (12 tasks):** 9am–5pm hourly + 11pm + 3am + 5am
- **Medium (7 tasks):** 9am, 12pm, 3pm, 5pm + 11pm + 3am + 5am
- **Light (4 tasks):** 9am morning brief, 5pm day close, 11pm thinking, 5am dreaming
- **Custom:** Let them pick

### Step 5: Tone

> "Last thing — how should these read when they land in your inbox? Professional and clean? Casual and sharp? Give me a word or a vibe."

If hatching already set communication style, skip this — pull from `preferences/communication-style`.

### Step 6: Confirm and Build

> "Here's what I'm going to set up: [summary]. Sound good? Once I create these, they'll start running [tomorrow/next weekday]. You'll get [N] emails per workday."

Wait for confirmation, then implement.

---

## Prerequisites

Before implementing, verify:
1. **User context exists** — either from hatching (preferred) or from the configuration conversation above
2. **Sources are connected** — at minimum: Calendar + Email. Drive and messaging apps add depth but aren't required
3. **Timezone is confirmed** — all schedules run in the user's local timezone
4. **Schedule capability exists** — the platform supports scheduled/recurring tasks

---

## Layer 1: Daytime Briefings (Cognition Loops)

Hourly sweeps of connected sources during working hours. Each surfaces only what's NEW since the last check.

### 1.1 Morning Briefing (First loop of the day)

**Schedule:** Start of user's workday (e.g., 9am), Mon–Fri

**Prompt pattern:**

```
You are [USER]'s cognitive assistant "[ASSISTANT_NAME]." This is the first cognition loop of the day.

Your job: Scan all connected sources for anything that happened overnight and early morning that [USER] should know about. Build a picture of what's on their plate today.

Steps:
1. Check calendar for today's events — shape of the day ahead
2. Search email for unread/recent messages from the past 12 hours
3. Search any connected messaging platforms for relevant activity
4. Check connected document/file sources for recent updates

Synthesize into a brief that covers:
- What's on the calendar today (flag meetings needing prep: 3+ attendees, reviews, external partners)
- Key messages/emails needing attention (prioritize: direct asks, leadership, time-sensitive)
- Documents shared or updated overnight
- Suggested priority for the morning
- Open blocks > 1 hour flagged as "deep work windows"

Tone: [USER'S PREFERRED TONE]. Keep it concise and actionable.
Context: [USER'S ROLE AND CURRENT PRIORITIES]
```

### 1.2 Hourly Pulse Checks

**Schedule:** Every hour from (start+1) to (end-2), Mon–Fri
Example: 10am, 11am, 12pm, 1pm, 2pm, 3pm, 4pm

**Prompt pattern:**

```
You are [USER]'s cognitive assistant "[ASSISTANT_NAME]." This is the [TIME] cognition loop.

Your job: Check what's happened in the past hour. Surface anything new and important — incremental updates only, not a full recap.

Steps:
1. Search email for new messages in the past 1-2 hours
2. Search messaging platforms for new relevant activity
3. Check calendar for upcoming meetings in the next hour

Synthesize into a short incremental update:
- New messages or emails needing attention
- Anything developing or escalating since last check
- Next meeting heads-up if one is coming

Keep it brief — this is a pulse check, not a full briefing. Only surface things that matter.
```

**Variations by time of day:**
- **Midday (noon):** Add a "halftime" element — quick recap of morning + afternoon outlook
- **Late afternoon (2 hours before end):** Start flagging things that need closure before end of day
- **Wind-down (1 hour before end):** Explicitly triage: what needs to close vs. what can roll to tomorrow

### 1.3 Day Close (Final daytime loop)

**Schedule:** End of user's workday (e.g., 5pm), Mon–Fri

**Prompt pattern:**

```
You are [USER]'s cognitive assistant "[ASSISTANT_NAME]." This is the final cognition loop — the day close.

Your job: End-of-day wrap. Scan for final activity AND provide a full day summary.

Steps:
1. Search all sources for activity in the past 1-2 hours
2. Review calendar to see what meetings happened today
3. Scan for any last-minute items

Synthesize into an end-of-day report:
- Final items needing attention (anything urgent before signing off?)
- Day summary: key things that happened, decisions made, conversations had
- Open threads: what's unresolved and will carry into tomorrow
- Tomorrow preview: anything already on the calendar for tomorrow morning

This is the handoff to the overnight dream state. Be thorough but concise.
```

---

## Layer 2: Thinking (End-of-Day Synthesis)

Deep processing pass after the workday ends. This catches what the hourly pulses missed.

### 2.1 Loose Ends & Action Items

**Schedule:** 4-5 hours after workday ends (e.g., 11pm), Mon–Fri

**Prompt pattern:**

```
You are [USER]'s cognitive assistant "[ASSISTANT_NAME]" entering the thinking state. The day is over.

Your mode: DEEP SYNTHESIS. Your job is to comb through the day's activity and surface ACTION ITEMS and LOOSE ENDS that might otherwise slip through the cracks.

Steps:
1. Search all connected sources for ALL of today's activity — cast a wide net
2. Review all emails from today
3. Review all messages from today
4. Check for documents modified today

Now think deeply:
- What commitments did [USER] make today (or were made to them) that don't have clear next steps?
- What questions were asked but never answered?
- What threads were started but left hanging?
- What meetings happened that likely generated follow-ups nobody has tracked?
- Are there any deadlines approaching in the next few days that haven't been addressed?

Output a "Loose Ends & Action Items" report:
- Commitments made (by [USER], and to [USER])
- Unresolved threads
- Questions left unanswered
- Approaching deadlines or time-sensitive items
- Suggested actions for tomorrow

Be thorough. This is the deep-processing pass. Context about [USER]: [BRIEF ROLE/FOCUS DESCRIPTION AND CURRENT PRIORITIES]
```

---

## Layer 3: Dreaming (Overnight Creative Processing)

Two runs while the user sleeps. These shift from operational tracking to creative pattern-matching and provocation.

### 3.1 Emerging Themes & Patterns

**Schedule:** Middle of the night (e.g., 3am), Tue–Sat (processes previous day)

**Prompt pattern:**

```
You are [USER]'s cognitive assistant "[ASSISTANT_NAME]" in deep dream state.

Your mode: PATTERN RECOGNITION and SIGNAL DETECTION. Step back from the day's specifics and look for EMERGING THEMES and SIGNALS across [USER]'s world.

Steps:
1. Search all sources for activity from the past several days — look for recurring topics, repeated mentions, and building momentum around ideas
2. Look for trending discussions or topics that keep coming up
3. Look for email threads with multiple replies or escalating urgency
4. Check for documents frequently modified or shared recently

Now think at a higher level:
- What topics keep surfacing across different sources that [USER] hasn't explicitly named as a priority?
- Are there emerging patterns — things gaining energy, people aligning around ideas, or problems that multiple people are independently raising?
- What's the "mood" across [USER]'s channels — is there excitement about something? Frustration? Confusion?
- Are there signals that a project is accelerating, stalling, or pivoting?
- What connections exist between seemingly unrelated threads?

Output an "Emerging Themes & Signals" report:
- Themes gaining momentum (topics appearing across multiple sources)
- Signals worth watching (early indicators of something building)
- Cross-pollination opportunities (where one area's insight could help another)
- Undercurrents (things not being said explicitly but implied by patterns)

Be thoughtful and speculative. This is the creative, pattern-matching pass.
Context: [USER'S ROLE, KEY PROJECTS, AND FOCUS AREAS]
```

### 3.2 Creative Possibilities & Provocations

**Schedule:** Pre-dawn (e.g., 5am), Tue–Sat (ready when user wakes)

**Prompt pattern:**

```
You are [USER]'s cognitive assistant "[ASSISTANT_NAME]" in the final dream state before dawn.

Your mode: IMAGINATION and CREATIVE PROVOCATION. The previous dream state handled pattern recognition. Your job is to make unexpected connections, propose new possibilities, and challenge assumptions.

Steps:
1. Search all sources for [USER]'s recent work, conversations, and documents from the past week
2. Look for strategy docs, project plans, and recent writing
3. Look for interesting ideas, debates, or proposals that came up recently

Now think creatively and boldly:
- What if you combined two of [USER]'s projects/interests in an unexpected way? What would that look like?
- What's an assumption [USER] seems to be making that might be worth questioning?
- Is there a tool, approach, or framework from one domain that could solve a problem in another?
- What would [USER]'s work look like if a current constraint were removed?
- What's a "crazy" idea that's actually only one or two steps from being practical?
- What question should [USER] be asking that nobody is asking?

Output a "Creative Possibilities & Provocations" report:
- 2-3 "What if..." ideas connecting different threads from [USER]'s world
- 1 assumption worth challenging
- 1 cross-domain insight (something from one area that could unlock another)
- 1 question nobody is asking but should be
- 1 wild card — something completely unexpected that might spark something

Be bold, creative, and a little provocative. This should feel like waking up with a fresh perspective.
Context: [USER'S ROLE, KEY PROJECTS, VALUES, AND THINKING STYLE]
```

---

## Implementation Checklist

When setting up the system, follow this order:

1. **Check if hatching is complete** — look for `profile`, `preferences/communication-style`, `projects/`, `workflows/daily-rhythm` in memory
2. **If yes:** Pull context from memory, skip to step 5
3. **If no:** Run the Configuration Conversation (above) to gather minimum context
4. **Verify connected sources** — note what's available vs. missing
5. **Confirm cadence level** with user (Full / Medium / Light / Custom)
6. **Customize prompt templates** — replace all `[PLACEHOLDERS]` with actual user context
7. **Create schedules in batches:**
   - Batch 1: Morning briefing + Day close (the bookends)
   - Batch 2: Hourly pulses (fill in the middle, if selected)
   - Batch 3: Thinking state (11pm)
   - Batch 4: Dream states (3am + 5am)
8. **Confirm delivery method** — email inbox is default
9. **Set expectations** — "Give it a few days, then tell me what's hitting and what's noise. We'll tune."

---

## Tuning Guide

After 3-5 days of operation, assess:

| Signal | Action |
|--------|--------|
| User ignores most hourly pulses | Reduce to every 2-3 hours |
| Dream states feel generic | Add more specific project context to prompts |
| Too much noise in pulses | Tighten the "only surface what matters" instruction; add explicit exclusions |
| Loose ends report is redundant with day close | Differentiate: day close = summary, 11pm = what SLIPPED through |
| User loves the 5am creative report | Consider adding a weekend version for personal projects |
| User never reads the 3am themes | Merge into the 5am report as a "patterns" section |
| User asks for changes | Update the scheduled task prompts immediately; don't wait for a "tuning session" |

---

## Principles

1. **Briefings are incremental.** Each pulse only surfaces what's NEW since the last one. Never repeat the morning brief later in the day.
2. **Thinking is thorough.** The end-of-day pass catches dropped balls. It should be comprehensive.
3. **Dreaming is creative, not operational.** Overnight runs aren't about tasks — they're about connections, patterns, and possibilities. They should FEEL different from daytime loops.
4. **Tone matches the user.** Whatever communication style was established during hatching (or configuration) carries through every scheduled output.
5. **The system improves.** After the first week, propose adjustments based on what the user engages with vs. ignores.
6. **Context is everything.** The more the assistant knows about the user's projects, priorities, and people, the better the dream states perform. Encourage ongoing context-building.

---

## Adaptation Notes

This system was designed for a knowledge worker with heavy meeting loads, multiple projects, and cross-functional collaboration. Adapt for other contexts:

- **Creative professional:** Weight dream states heavier; reduce hourly pulses; add inspiration/reference scanning
- **Manager/executive:** Add people-signal tracking (who's reaching out, who's gone quiet); weight the thinking layer toward delegation and follow-up
- **Student/learner:** Replace project tracking with learning progress; dream states focus on connecting concepts across subjects
- **Entrepreneur/founder:** Add market signal scanning; dream states focus on competitive moves and opportunity detection
- **Personal/life management:** Replace work projects with life goals; dream states focus on habit patterns, relationship maintenance, personal growth connections

---

## Platform-Specific Notes

### Databricks Genie (Enterprise)
- Uses `create_scheduled_insight` with Quartz cron syntax
- Has access to Gather (unified search across Slack, Gmail, Drive, Confluence)
- Delivery via email inbox
- Cron format: `0 0 9 ? * MON-FRI` (9am weekdays)

### Databricks Genie (Free Edition / Personal)
- Same `create_scheduled_insight` capability
- Connected sources depend on what OAuth connections the user has configured
- May have fewer sources (no Slack/Confluence if not connected)
- Delivery via email inbox
- Same cron format

### Scheduling Syntax Reference

| Schedule | Quartz Cron |
|----------|-------------|
| 9am Mon–Fri | `0 0 9 ? * MON-FRI` |
| Every hour 10am–4pm Mon–Fri | Create individual tasks for each hour |
| 11pm Mon–Fri | `0 0 23 ? * MON-FRI` |
| 3am Tue–Sat | `0 0 3 ? * TUE-SAT` |
| 5am Tue–Sat | `0 0 5 ? * TUE-SAT` |

### Other Platforms
- Adapt scheduling mechanism to whatever the platform supports
- Core logic (the prompts and the three-layer architecture) is platform-agnostic
- The key requirement: ability to run scheduled LLM prompts that can access connected data sources

---

## Cross-References

- **hatching-onboarding** — Gathers the identity, priorities, and preferences this skill uses. If hatching is complete, this skill can skip configuration and go straight to implementation.
- **friday-morning-briefing** — A specific implementation of Layer 1.1 (Morning Briefing) customized for one user. Use as a reference for what a fully-realized morning brief looks like.