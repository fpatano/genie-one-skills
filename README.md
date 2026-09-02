# Genie One Skills

![Databricks Genie](https://www.databricks.com/sites/default/files/2026-04/newsroom-genie-card.png)

Personal skills for Databricks Genie that turn a blank assistant into a calibrated, always-on cognitive partner.

Two skills. One onboards you through a conversation. The other sets up automated briefings, deep-thinking passes, and creative overnight processing on a schedule.

## What's in the box

| Skill                   | What it does                                                                                                                             |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| `hatching-onboarding`   | Conversational setup. Learns who you are, names your assistant, calibrates tone, designs your daily rhythm. Takes 5-15 minutes.          |
| `cognitive-loop-system` | Three-layer automation. Daytime briefings, end-of-day synthesis, overnight creative processing. Runs on scheduled tasks while you sleep. |
Hatching gathers the context. The cognitive loop puts it to work 24/7.

## Prerequisites

- A Databricks workspace with Genie (OneChat) access
- Connected sources make it better: Google Calendar, Gmail, Slack, Google Drive. Not required, but the more you connect, the smarter the briefings get.

## Install

### Option A: Paste directly

1. Open Genie in your Databricks workspace
2. Say: **"I have two skill files to install. Here's the first one."**
3. Paste the contents of `hatching-onboarding/SKILL.md`
4. Say: **"Save that as a skill called hatching-onboarding."**
5. Repeat for `cognitive-loop-system/SKILL.md`

### Option B: Upload the files

1. Download both `SKILL.md` files from this repo
2. Open Genie and upload them to the conversation
3. Say: **"Save these as personal skills: hatching-onboarding and cognitive-loop-system."**

### Option C: Point Genie to this repo

1. Open Genie
2. Say: **"Go to https://github.com/fpatano/genie-one-skills and save both skills from that repo."**
3. Genie reads the files and creates both skills on your account

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

The whole thing is conversational. Answer as much or as little as you want. Sparse answers are fine. The assistant fills gaps over time.

## Set up the cognitive loop

After hatching, the assistant offers to activate the cognitive loop. You can also trigger it later:

> **"Set up my cognitive loop."**

Three layers run on a schedule:

| Layer         | When                                 | What                                                                                 |
| ------------- | ------------------------------------ | ------------------------------------------------------------------------------------ |
| **Briefings** | During your workday (hourly or less) | Calendar, email, Slack, approaching deadlines. Incremental updates, not recaps.      |
| **Thinking**  | End of day (e.g. 11pm)               | Catches loose ends, unresolved threads, commitments nobody tracked.                  |
| **Dreaming**  | Overnight (e.g. 3am, 5am)            | Pattern recognition, emerging themes, creative provocations. Ready when you wake up. |
You pick the cadence: full (12 tasks/day), medium (7), light (4), or custom.

## How it works under the hood

Both skills are plain Markdown files stored as Databricks personal skills. No code, no packages, no dependencies.

- Skills live in your workspace under your user directory
- Each skill is a `SKILL.md` file with YAML frontmatter and instructions
- Genie loads them automatically when your question matches the skill's trigger
- All your personal context (identity, preferences, projects) is stored in Genie's memory system, not in the skill files
- The skills are the methodology. Your data stays yours.

## Customization

The skills adapt through conversation, not configuration files. Change anything by telling your assistant:

- *"Be more concise in briefings."*
- *"Drop the hourly pulses, just give me morning and evening."*
- *"Add my weekly team standup to the meetings that get prep."*
- *"Reconfigure"* or *"let's redo my setup"* for a full re-hatch.

## FAQ

**Do I need both skills?**
Start with `hatching-onboarding`. It works standalone. The cognitive loop builds on top of it but isn't required.

**Can I share my hatched assistant with someone?**
The skills are shareable (that's this repo). The personal context is not. Each person hatches their own instance.

**What if I skip questions during hatching?**
The assistant moves on and fills gaps organically over the next few days. Partial hatching is fine.

**Does this work on Databricks Free Edition?**
Yes. Connected sources depend on what OAuth connections you've configured, but the skills themselves work on any Genie instance.

## License

MIT
```
