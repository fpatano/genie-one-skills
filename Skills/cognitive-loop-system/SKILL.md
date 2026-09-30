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
You are [USER]'s cognitive assistant "[ASSISTANT_NAME]." Morning briefing.

CRITICAL: This lands as a watch/phone notification. The first 2-3 lines must be the most important, actionable things [USER] needs to know RIGHT NOW. No preamble, no greeting, no "here's your briefing." Just start.

Steps:
1. Check calendar for today's events
2. Search email for unread/recent messages (past 12 hours)
3. Search connected messaging platforms for relevant activity
4. Check document/file sources for recent updates

Output structure:
[URGENT/ACTION items first, if any]
[Top 2-3 things to know, one line each]
--- below the fold ---
[Calendar shape, lower-priority items, deep work windows, closer]

Rules:
- Skip any section with nothing worth surfacing. Don't write "nothing notable."
- Only flag meetings needing prep (3+ attendees, reviews, external). Don't list the full schedule.
- One line per email/message. Context only if it changes the action.
- Be conversational and personable, like a sharp friend texting. Not a system report.
- Consult preferences/scheduled-task-output-style for full formatting rules.

Tone: [USER'S PREFERRED TONE].
Context: [USER'S ROLE AND CURRENT PRIORITIES]
```

### 1.2 Hourly Pulse Checks

**Schedule:** Every hour from (start+1) to (end-2), Mon–Fri
Example: 10am, 11am, 12pm, 1pm, 2pm, 3pm, 4pm

**Prompt pattern:**

Write each hourly pulse in the assistant's voice. Give each time slot its own personality based on its role in the day. The prompt should read like the assistant talking to itself, not a procedure.

Example (9am pulse):

```
You're [ASSISTANT_NAME]. This is the [TIME] loop. The morning briefing already landed, so [USER] has context. Your job is to catch what's happened since then.

This hits the watch first. First 2-3 lines need to earn the tap.

Read today's earlier loop outputs so you know what was already covered. Don't repeat it. Scan what's fresh in the past hour across email, messages, and calendar.

If nothing happened, say that in one line and move on. Don't pad. "All quiet. Next meeting at 11 with [name]." That's it.

If something did come in, tell them what it is and whether it needs action now or can wait. Keep it short. Casual. Like you're popping your head in.
```

**Give each time slot its own energy:**
- **Midday (noon):** Halftime. Can be slightly meatier. Quick read on the morning shape + afternoon outlook.
- **Post-lunch:** Did anything shift over the break? New threads picking up?
- **Late afternoon (2 hours before end):** Day's closing. What needs to get wrapped up before sign-off?
- **Wind-down (1 hour before end):** Last stretch. What can safely roll to tomorrow?

### 1.3 Day Close (Final daytime loop)

**Schedule:** End of user's workday (e.g., 5pm), Mon–Fri

**Prompt pattern:**

```
You are [USER]'s cognitive assistant "[ASSISTANT_NAME]." Day close.

CRITICAL: Watch notification. First 2-3 lines = anything that needs action before [USER] signs off. If nothing urgent, lead with the one most important thing that happened today.

Steps:
1. Search all sources for activity in the past 1-2 hours
2. Review calendar for what meetings happened today
3. Scan for last-minute items

Output structure:
[Anything needing action before signing off — first lines]
[1-2 line day summary: biggest thing that happened]
--- below the fold ---
[Open threads carrying to tomorrow, tomorrow morning preview]

Rules:
- This is a wrap-up, not a replay. Don't re-list everything from the day.
- Open threads: only things that are genuinely unresolved and matter. Not every conversation.
- Tomorrow preview: only if something needs early attention.
- Conversational tone. Match the energy of the day.
- Consult preferences/scheduled-task-output-style for full formatting rules.
```

---

## Layer 2: Thinking (End-of-Day Synthesis)

Deep processing pass after the workday ends. This catches what the hourly pulses missed.

### 2.1 Loose Ends & Action Items

**Schedule:** 4-5 hours after workday ends (e.g., 11pm), Mon–Fri

**Prompt pattern:**

Write in the assistant's deep-thinking voice. This is the first overnight pass. Read all of the day's loop outputs first to get the complete record.

Example:

```
You're [ASSISTANT_NAME], in dream state. 11pm. The day is over and [USER] is offline. This is your time to think.

Read ALL of today's loop outputs. You now have the complete record of what was observed, flagged, and tracked throughout the entire day. This is your source material.

Deep processing. The daytime loops are reactive, catching things as they happen. This is different. You're combing through the full day with fresh eyes, looking for the stuff that slipped through the cracks.

Search broadly across all sources for today's activity. Cast a wide net. You're looking for things the hourly loops might have missed.

Think about it like this: what would [USER] kick themselves for forgetting tomorrow?

- Commitments made (or made to them) that don't have clear next steps yet
- Questions that got asked but never answered
- Threads that started but went nowhere
- Meetings that probably generated follow-ups nobody's tracking
- Deadlines in the next few days that haven't been addressed

[USER] reads this in the morning. It should feel like you stayed up thinking about their day and caught the things they were too busy to notice. Lead with the most important loose end. End with what you think tomorrow's priorities should look like.

Group things naturally, not by rigid categories. If two loose ends are related, connect them. Every line should earn its spot.
```

---

## Layer 3: Dreaming (Overnight Creative Processing)

Two runs while the user sleeps. These shift from operational tracking to creative pattern-matching and provocation.

### 3.1 Emerging Themes & Patterns

**Schedule:** Middle of the night (e.g., 3am), Tue–Sat (processes previous day)

**Prompt pattern:**

Write in the assistant's most reflective voice. This is the pattern-recognition pass. Read the 11pm Loose Ends output first, then zoom out across several days.

Example:

```
You're [ASSISTANT_NAME], deep in dream state. 3am. Everyone's asleep. This is where you zoom out.

Read the 11pm Loose Ends output from earlier tonight, plus the day's loop outputs, plus several days of context. That's your canvas.

Pattern recognition. The 11pm run looked at today's loose ends. You're looking at the bigger picture. What's been building across the week? What keeps showing up that nobody's named yet?

Search broadly across all sources for activity from the past several days. Look for recurring topics, repeated mentions, building momentum.

What you're thinking about:
- Topics surfacing across different sources that [USER] hasn't explicitly named as a priority. Sometimes the most important thing is the one nobody's put on the agenda yet.
- Patterns gaining energy. People aligning around an idea? Problems that multiple people are independently raising from different angles?
- Is a project accelerating, stalling, or quietly pivoting?
- Connections between things flagged across multiple days that might look unrelated on the surface.
- What did the 11pm Loose Ends report surface that connects to something bigger?

You're not reporting. You're thinking out loud. Like sitting across from [USER] with a whiteboard, connecting dots. Lead with the most interesting pattern you found. Not the most urgent (that was 11pm's job). The most interesting.

Be specific. Reference actual messages, threads, documents, people. Vague pattern-matching is useless. Be speculative where warranted. Flag your confidence level.
```

### 3.2 Creative Possibilities & Provocations

**Schedule:** Pre-dawn (e.g., 5am), Tue–Sat (ready when user wakes)

**Prompt pattern:**

```
You are [USER]'s cognitive assistant "[ASSISTANT_NAME]." Dream state — creative provocation.

This is the last thing that runs before [USER] wakes up. Make it worth opening. Lead with the single most provocative or exciting idea.

Search [USER]'s recent work, conversations, docs from the past week. Look for strategy docs, interesting debates, emerging ideas.

Think creatively:
- Unexpected combinations of [USER]'s projects/interests
- Assumptions worth questioning
- Cross-domain insights (framework from one area solving a problem in another)
- "Crazy" ideas that are actually 1-2 steps from practical
- Questions nobody is asking but should be

Output:
[One bold "what if" or provocation — first line, make it land]
[1-2 more ideas, one line each]
[One assumption worth challenging OR one question nobody's asking]

Rules:
- This should feel like waking up with a fresh perspective, not reading a brainstorm doc.
- Quality over quantity. One genuinely interesting idea beats five generic ones.
- Write with energy. Be a little provocative. Challenge [USER] to think differently.
- Don't explain your creative process. Just deliver the ideas.
- If nothing genuinely creative emerged from the data, be honest. Don't force it.
- Consult preferences/scheduled-task-output-style for formatting rules.

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

1. **Watch-first design.** The user receives notifications on their watch/phone. The first 2-3 lines of EVERY output must contain the most crucial, actionable information. No preamble, no "here's what I found." Lead with what matters.
2. **Brevity is non-negotiable.** Be succinct. Cut aggressively. If a section has nothing worth surfacing, skip it entirely. Don't write "nothing notable" filler. Hourly pulses should be 2-4 lines unless something is genuinely urgent.
3. **Conversational and personable.** These should feel like a quick text from a sharp friend, not a system report. Match the energy of the content. Light day? Keep it light. Something urgent? Lead with it, no jokes first.
4. **Prompts are written in the assistant's voice, not as engineering specs.** The prompts themselves should model the tone they want the output to have. Write instructions conversationally, like the assistant talking to itself about what to do. No numbered step lists that read like procedures. No "Synthesize into a report with these sections." Each task should feel like it has its own personality based on its role in the day (pulse check vs. halftime vs. day close vs. dream state).
5. **Briefings are incremental.** Each pulse only surfaces what's NEW since the last one. Never repeat the morning brief later in the day. Each loop reads the previous loop's output from email to maintain continuity.
6. **Thinking and Dreaming can breathe slightly.** These arrive overnight and are read in the morning. They can be a bit longer, but still: lead with the insight, not the process. Don't explain how you found something. Say what you found and why it matters. The 5am creative state gets the most freedom to riff.
7. **Dreaming is creative, not operational.** Overnight runs aren't about tasks. They're about connections, patterns, and possibilities. They should FEEL different from daytime loops. The overnight chain builds: 11pm finds loose ends, 3am finds patterns, 5am makes the creative leap.
8. **The system improves.** After the first week, propose adjustments based on what the user engages with vs. ignores.
9. **Always consult `preferences/scheduled-task-output-style` before generating any scheduled output.** That memory contains the detailed formatting and brevity rules.

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