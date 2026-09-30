# Genie One Skills

![Databricks Genie](https://www.databricks.com/sites/default/files/2026-04/newsroom-genie-card.png)

Personal skills for Databricks Genie that turn a blank assistant into a calibrated, always-on cognitive partner.

Three shareable skills. One onboards you through a conversation. One sets up automated briefings and overnight processing. One gives you a framework for auditing AI-generated documents. Together, they form a system that gets smarter over time.

## What's in the box

| Skill | What it does |
| --- | --- |
| `hatching-onboarding` | Conversational setup. Learns who you are, names your assistant, calibrates tone, designs your daily rhythm. Takes 5-15 minutes. |
| `cognitive-loop-system` | Three-layer automation. Daytime briefings, end-of-day synthesis, overnight creative processing. Runs on scheduled tasks while you sleep. |
| `audit-ai-output` | Quality framework for AI-generated documents. Grades on declared scope, classifies findings by actionability, applies human-reality checks for time, capacity, and organizational friction. |

Hatching gathers the context. The cognitive loop puts it to work 24/7. The audit framework keeps quality honest.

## What hatching builds for you

Beyond the three installable skills, the hatching process can **build two additional capabilities** personalized to your world:

| Capability | Built from | What it does |
| --- | --- | --- |
| **Communication Wrapper** | Your Phase 4 style preferences | Classifies response types (casual, content production, analytical, instructional) and applies your specific tone rules, pet peeves, and persona consistently across everything. |
| **Priority Triage** | Your Phase 3 priorities + Phase 5 sources | On-demand "what should I focus on next?" that cross-references your project tracker, calendar, email, and Slack. Verifies completion claims against actual activity. |

These can't be pre-built because they need *your* channels, *your* project tracker, *your* manager's name, *your* style rules. The hatching skill teaches the assistant how to create them from your context.

## Prerequisites

- A Databricks workspace with Genie One access
- Connected sources make it better: Google Calendar, Gmail, Slack, Google Drive. Not required, but the more you connect, the smarter the briefings get.

## Install

### Option A: Paste directly

1. Open Genie in your Databricks workspace
2. Say: **"I have a skill file to install. Here's the content."**
3. Paste the contents of `Skills/SKILL (hatching).md`
4. Say: **"Save that as a skill called hatching-onboarding."**
5. Repeat for `cognitive-loop-system` and `audit-ai-output`

### Option B: Upload the files

1. Download the `SKILL.md` files from this repo
2. Open Genie and upload them to the conversation
3. Say: **"Save these as personal skills."**

### Option C: Point Genie to this repo

1. Open Genie
2. Say: **"Go to https://github.com/fpatano/genie-one-skills and save the skills from that repo."**
3. Genie reads the files and creates the skills on your account

> **Note:** Option C requires GitHub to be connected as an MCP source in your workspace. If it's not, use Option A or B.

## Start hatching

Once the skills are saved, start a new conversation and say:

> **"Let's hatch."**

The assistant walks you through six phases:

1. **First Contact** — decides whether to set up now or later
2. **Identity** — learns your name, role, team, timezone
3. **Priorities** — current projects, goals, blockers
4. **Working Style** — communication tone, format, persona name
5. **Daily Rhythm** — morning briefings, meeting prep, end-of-day wraps
6. **The Hatch** — activates your configured assistant

The whole thing is conversational. Answer as much or as little as you want.

## Set up the cognitive loop

After hatching, the assistant offers to activate the cognitive loop. You can also trigger it later:

> **"Set up my cognitive loop."**

Three layers run on a schedule:

| Layer | When | What |
| --- | --- | --- |
| **Briefings** | During your workday (hourly or less) | Calendar, email, Slack, approaching deadlines. Incremental updates, not recaps. |
| **Thinking** | End of day (e.g. 11pm) | Catches loose ends, unresolved threads, commitments nobody tracked. |
| **Dreaming** | Overnight (e.g. 3am, 5am) | Pattern recognition, emerging themes, creative provocations. Ready when you wake up. |

You pick the cadence: full (12 tasks/day), medium (7), light (4), or custom.

## Upgrade path

Already hatched but want more? Say:

> **"What else can you do?"** or **"Level up."**

The assistant assesses what's active and recommends the highest-impact next capability to build. It introduces them one at a time so you can experience each before adding more.

## How it works under the hood

All skills are plain Markdown files stored as Databricks personal skills. No code, no packages, no dependencies.

- Skills live in your workspace under your user directory
- Each skill is a `SKILL.md` file with YAML frontmatter and instructions
- Genie loads them automatically when your question matches the skill's trigger
- All your personal context (identity, preferences, projects) is stored in Genie's memory system, not in the skill files
- The skills are the methodology. Your data stays yours.

## Sharing and publishing

These three skills are designed to be shared as-is. The communication wrapper and priority triage capabilities are built per-user during hatching because they require personal context.

**Current sharing options:**
- This GitHub repo (copy/paste, upload, or direct repo read)
- Manual file sharing (they're just Markdown)

**Coming soon:**
- Unity Gateway Skills (Beta) lets you publish skills to Unity Catalog as governed assets with permissions. Once GA, that becomes the formal distribution channel.

## FAQ

**Do I need all three skills?**
Start with `hatching-onboarding`. It works standalone. The cognitive loop and audit framework build on top of it but aren't required.

**Can I share my hatched assistant with someone?**
The skills are shareable (that's this repo). The personal context is not. Each person hatches their own instance.

**What about the communication wrapper and priority triage?**
Those get built during hatching, personalized to your world. The hatching skill teaches the assistant how to create them from your specific context.

**What if I skip questions during hatching?**
The assistant moves on and fills gaps organically over the next few days.

**Does this work on Databricks Free Edition?**
Yes. Connected sources depend on what OAuth connections you've configured, but the skills themselves work on any Genie instance.

## License

MIT